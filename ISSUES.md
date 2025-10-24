# Issue Tracking & Bug Reports

## 📋 Executive Summary

This document aggregates performance advisor issue types and critical bugs to help the development team triage and fix performance problems discovered in the Supabase project. Issues are categorized by severity and include specific remediation steps with SQL examples.

**Related Documentation:**
- **Setup & Development**: [README.md](./README.md)
- **Deployment Guide**: [DEPLOYMENT.md](./DEPLOYMENT.md)
- **Project Handoff**: [Handoff.md](./Handoff.md)

## 🚨 Critical Issues (Must Fix Before Production)

### 1. Apple App Store Compliance Violations
- **Issue**: Internal admin dashboard violates Apple Developer Subscription terms
- **Impact**: App will be rejected from App Store
- **Priority**: Critical
- **Status**: Open
- **Solution**: Remove admin dashboard and create external data management system
- **Timeline**: Immediate (before any App Store submission)

### 2. Hard-coded Data Implementation
- **Issue**: Business data is hard-coded instead of dynamic
- **Impact**: Cannot update content without code changes
- **Priority**: Critical
- **Status**: Open
- **Solution**: Replace with API-driven data from external admin system
- **Timeline**: Immediate (before any App Store submission)

## ⚠️ High Priority Supabase Performance Issues

### 1. RLS Policy Auth Function Re-evaluation (Critical Performance Impact)
- **Issue**: `auth.uid()` and `current_setting()` calls re-evaluated per row in RLS policies
- **Schema/Table**: `public.businesses`
- **Policies Affected**: 
  - "Staff can view all businesses"
  - "Moderators can update businesses" 
  - "Moderators can delete businesses"
- **Severity**: High
- **Root Cause**: Function calls in policy expressions executed for each row, causing high CPU usage
- **Impact**: Poor query performance at scale, potential database blocking
- **Recommended Fix**:
  ```sql
  -- Replace auth.uid() with scalar subquery
  -- BEFORE (problematic):
  USING (auth.uid() = some_column)
  
  -- AFTER (optimized):
  USING ((SELECT auth.uid()) = some_column)
  
  -- For current_setting():
  -- BEFORE:
  USING (current_setting('jwt.claims.tenant_id') = some_value)
  
  -- AFTER:
  USING ((SELECT current_setting('jwt.claims.tenant_id')) = some_value)
  ```
- **Validation**: Run `EXPLAIN ANALYZE` on representative queries; confirm reduced per-row function calls
- **Owner**: DB Team
- **Timeline**: Immediate

### 2. Multiple Permissive RLS Policies (Performance Degradation)
- **Issue**: Multiple permissive policies for same role/action combinations
- **Schema/Table**: `public.businesses`
- **Affected Combinations**:
  - `anon` + `INSERT`: "Allow public insert access", "Allow public insert access to businesses"
  - `anon` + `SELECT`: "Allow public read approved businesses", "Public can view approved businesses", "Staff can view all businesses"
  - `authenticated` + `DELETE`: "Allow authenticated users full access", "Moderators can delete businesses"
  - `authenticated` + `INSERT`: "Allow authenticated users full access", "Allow public insert access", "Allow public insert access to businesses"
  - `authenticated` + `SELECT`: "Allow authenticated users full access", "Allow public read approved businesses", "Public can view approved businesses", "Staff can view all businesses"
  - `authenticated` + `UPDATE`: "Allow authenticated users full access", "Moderators can update businesses"
  - `authenticator` + `INSERT`: "Allow public insert access", "Allow public insert access to businesses"
  - `authenticator` + `SELECT`: "Allow public read approved businesses", "Public can view approved businesses", "Staff can view all businesses"
  - `dashboard_user` + `INSERT`: "Allow public insert access", "Allow public insert access to businesses"
  - `dashboard_user` + `SELECT`: "Allow public read approved businesses", "Public can view approved businesses", "Staff can view all businesses"
- **Severity**: High
- **Root Cause**: Each permissive policy must be executed for every relevant query
- **Impact**: Suboptimal performance as multiple policies evaluated per query
- **Recommended Fix**:
  ```sql
  -- Consolidate policies by role and action
  -- Example for anon SELECT:
  DROP POLICY "Allow public read approved businesses" ON businesses;
  DROP POLICY "Public can view approved businesses" ON businesses;
  DROP POLICY "Staff can view all businesses" ON businesses;
  
  -- Create single consolidated policy
  CREATE POLICY "anon_select_businesses"
    ON businesses
    FOR SELECT
    TO anon
    USING (status = 'approved');
  ```
- **Validation**: Confirm single policy per role/action combination
- **Owner**: DB Team
- **Timeline**: Immediate

### 3. Missing Indexes on Policy/Filter Columns
- **Issue**: Queries and RLS policies filter on columns that are not indexed
- **Schema/Table**: `public.businesses`
- **Likely Columns**: `status`, `created_at`, `updated_at`, `user_id` (if exists)
- **Severity**: High
- **Root Cause**: No index present on frequently filtered columns
- **Impact**: Sequential scans instead of index scans, poor query performance
- **Recommended Fix**:
  ```sql
  -- Create indexes on commonly filtered columns
  CREATE INDEX CONCURRENTLY idx_businesses_status ON businesses(status);
  CREATE INDEX CONCURRENTLY idx_businesses_created_at ON businesses(created_at);
  CREATE INDEX CONCURRENTLY idx_businesses_updated_at ON businesses(updated_at);
  
  -- For multi-column filters, consider composite indexes
  CREATE INDEX CONCURRENTLY idx_businesses_status_created_at 
    ON businesses(status, created_at);
  ```
- **Validation**: Run `EXPLAIN ANALYZE` on representative queries; confirm index usage and reduced cost
- **Owner**: DB Team
- **Timeline**: Immediate

## 🔧 Medium Priority Issues

### 4. RLS Policy Misconfigurations
- **Problem**: Tables with RLS enabled block access unexpectedly (returns no rows)
- **Cause**: Missing or overly restrictive policies, or policies granted to the wrong role
- **Impact**: Users cannot access data they should be able to see
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Use explicit policies per operation, test as anon/authenticated/staff, use helper functions for complex checks, and index columns used in policies

### 5. Overly Broad Policies (Service Role Leaks)
- **Problem**: Service keys hardcoded or used in client-side code give full DB access to attackers
- **Cause**: Using SUPABASE_SERVICE_ROLE_KEY in client apps or exposing it in repos
- **Impact**: Complete database compromise if exposed
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Never expose service keys publicly; use Edge Functions or server-side code with service keys; use anon keys in clients

### 6. Realtime Scaling and postgres_changes Pitfalls
- **Problem**: postgres_changes-based realtime subscriptions don't scale and can block the DB
- **Cause**: postgres_changes is single-threaded and not intended for high-volume apps
- **Impact**: Performance degradation and potential database blocking
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Use realtime.broadcast (with DB triggers) and private channels; follow channel/topic naming and RLS on realtime.messages

### 7. Authentication / JWT Claim Mismatches
- **Problem**: auth.uid() returns NULL or custom claims aren't present in DB session
- **Cause**: Missing Authorization header, misconfigured JWT, or custom claims not mapped correctly
- **Impact**: Authentication failures and access control issues
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Ensure client sets auth header, verify JWT via jwt.io, refresh tokens, and use auth.jwt() for custom claims. Test functions that rely on auth.uid()

### 8. Storage Object Access Problems
- **Problem**: Users can't access files despite correct bucket rules
- **Cause**: Wrong RLS policies on storage.objects, incorrect folder naming assumptions, or missing signed URLs
- **Impact**: Images and files not loading for users
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Use correct storage.objects policies (see example patterns), verify foldername(name) semantics, and generate signed URLs where appropriate

### 9. Indexing and Performance Regressions
- **Problem**: Slow queries after adding RLS or complex policies
- **Cause**: Missing indexes on columns referenced in policies or WHERE clauses; policies forcing sequential scans
- **Impact**: Poor user experience due to slow loading
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Add indexes on policy/filter columns (user_id, tenant_id, status), avoid unnecessary JOINs in policy expressions (use helper functions or subqueries), and EXPLAIN ANALYZE queries

### 10. Edge Function Deployment/Runtime Issues
- **Problem**: Functions fail in hosted environment but work locally
- **Cause**: Missing environment variables, version mismatches, or using unsupported third-party imports
- **Impact**: Broken functionality in production
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Use SUPABASE_* env vars provided automatically, prefer Web APIs/Deno core, use npm: specifier with versions, and test with the same runtime as production

### 11. Admin Dashboard & CLI Permission Constraints
- **Problem**: Some operations fail due to organization-level settings or missing permissions
- **Cause**: Organization privacy/opt-in settings block debug tools (e.g., assistant tools), or user account lacks project permissions
- **Impact**: Cannot perform administrative tasks
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Check organization settings and role permissions; perform actions with a user who has project owner privileges

### 12. Supabase Realtime Private Channel Authorization Problems
- **Problem**: Clients cannot subscribe to private channels or receive broadcasts
- **Cause**: RLS policies on realtime.messages/authorization mismatch; private channels require proper session/auth setup
- **Impact**: Real-time features not working
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Ensure policies on realtime.messages allow SELECT/INSERT for authenticated users and that channels are created with private: true and the client sets auth before subscribing

### 13. Migration / Schema Drift and Extension Issues
- **Problem**: Migrations fail or extensions unavailable across environments
- **Cause**: Using extensions not installed in target DB, or migration order issues
- **Impact**: Deployment failures and inconsistent environments
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Ensure required extensions are listed and installed (check pg_extension), run migrations in correct order, and test migrations in CI/staging

### 14. Rate Limits & Quota Surprises
- **Problem**: Unexpected rate limiting or project usage caps affecting behavior
- **Cause**: Hitting API or storage limits for a plan
- **Impact**: Service interruptions and degraded functionality
- **Priority**: Medium
- **Status**: Open
- **Mitigation**: Monitor usage in dashboard, implement client-side rate limiting, and upgrade plan if needed

## 🔧 Low Priority Issues (Nice to Fix)

### 1. Environment Configuration
- **Issue**: Missing `.env.example` file
- **Impact**: New developers may struggle with setup
- **Priority**: Low
- **Status**: Open
- **Solution**: Create `.env.example` with placeholder values

### 2. Error Handling
- **Issue**: Limited error boundary coverage
- **Impact**: Potential unhandled errors in production
- **Priority**: Low
- **Status**: Open
- **Solution**: Add try-catch blocks around async operations and improve error logging

### 3. Type Safety
- **Issue**: Some `any` types in TypeScript
- **Impact**: Reduced type safety and potential runtime errors
- **Priority**: Low
- **Status**: Open
- **Solution**: Add proper TypeScript interfaces for all data structures

### 4. Debug Logging
- **Issue**: Debug console.log statements in production code
- **Location**: `src/components/DirectoryPage.tsx:401`
- **Impact**: Console clutter in production
- **Priority**: Low
- **Status**: Open
- **Solution**: Remove or wrap in development-only conditions

### 5. Legal Document Placeholders
- **Issue**: Privacy Policy and Terms of Service contain placeholder content
- **Location**: `src/components/PrivacyPolicyPage.tsx` and `src/components/TermsOfServicePage.tsx`
- **Impact**: Legal documents need to be customized for specific use case
- **Priority**: Low
- **Status**: Open
- **Solution**: Review and customize legal content, update "Last Updated" dates as needed
- **Note**: These files are easily editable and can be updated at any time

## 📊 Issue Statistics

- **Total Issues**: 19
- **Critical**: 2
- **High Priority**: 3
- **Medium Priority**: 9
- **Low Priority**: 5

## 🎯 Resolution Timeline

### Immediate (Week 1)
- [ ] Apple App Store compliance issues
- [ ] Hard-coded data replacement
- [ ] **RLS Policy Auth Function Re-evaluation fixes**
- [ ] **Multiple Permissive RLS Policies consolidation**
- [ ] **Missing Indexes on Policy/Filter Columns**
- [ ] Service role security fixes

### Short Term (Month 1)
- [ ] Authentication/JWT fixes
- [ ] Storage access problems
- [ ] Performance optimizations
- [ ] Environment configuration

### Long Term (Quarter 1)
- [ ] Realtime scaling improvements
- [ ] Edge function stability
- [ ] Migration process improvements
- [ ] Type safety improvements

## 🔍 Testing Status

### Current Test Coverage
- **Unit Tests**: Not implemented
- **Integration Tests**: Not implemented
- **E2E Tests**: Not implemented
- **Manual Testing**: Basic functionality tested

### Testing Priorities
1. **Authentication Flow**: Test all auth scenarios
2. **RLS Policies**: Test with different user roles
3. **Data Access**: Verify all CRUD operations
4. **Performance**: Load testing and optimization
5. **Security**: Penetration testing and vulnerability scanning

## 📝 Issue Reporting

### How to Report New Issues
1. Check if the issue already exists in this document
2. If new, add it to the appropriate section
3. Include:
   - Clear description
   - Steps to reproduce
   - Expected vs actual behavior
   - Priority level
   - Proposed solution

### Issue Template
```markdown
### [Issue Title]
- **Problem**: [Description]
- **Cause**: [Root cause analysis]
- **Impact**: [What this affects]
- **Priority**: [Critical/High/Medium/Low]
- **Status**: [Open/In Progress/Resolved]
- **Solution**: [Proposed fix]
- **Timeline**: [When to fix]
```

## 🔄 Issue Lifecycle

1. **Open**: Issue identified and documented
2. **In Progress**: Work has begun on the issue
3. **Testing**: Fix implemented, under testing
4. **Resolved**: Issue fixed and verified
5. **Closed**: Issue no longer relevant

## 📞 Support Contacts

- **Supabase Issues**: [Supabase Support](https://supabase.com/support)
- **Apple Developer Issues**: [Apple Developer Support](https://developer.apple.com/support/)
- **General Technical Issues**: Create GitHub issue or contact project maintainer

## 🏷️ Suggested Labels

- `perf` - Performance related issues
- `rls` - Row Level Security issues
- `index` - Database indexing issues
- `urgent` - Critical issues requiring immediate attention
- `low-risk` - Low priority issues
- `db-schema` - Database schema related
- `realtime` - Realtime functionality issues

## 👥 Suggested Owners

- **DB Team**: Schema/index changes, RLS policy fixes
- **Backend Team**: Application query changes, authentication fixes
- **Infrastructure Team**: Autovacuum, resource tuning, monitoring

---

*This document should be updated regularly as issues are discovered, resolved, or new ones arise. Last updated: October 24, 2025*
