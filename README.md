# RCBMC Directory - iOS Beta Version

A comprehensive community directory for discovering and supporting Black, Minority, Women, Veteran, Latino, Immigrant, Asian, and LGBTQ+ owned businesses in Richmond County.

## 🚀 Features

### Public Features
- **Business Directory**: Search and filter businesses by category, certifications, and location
- **Business Submission**: Self-service business listing submission
- **Newsletter Signup**: Community updates and event notifications
- **Events Calendar**: Upcoming chamber events and networking opportunities
- **Business Spotlight**: Featured business of the week
- **Responsive Design**: Mobile-first, iOS-ready interface

## 📊 Data Management System (Incomplete – External Admin Required)

### Overview
The data management and admin functionality is not yet implemented.  
All admin-related features are currently disabled for Apple App Store compliance. Only example code remains within the project as references for the development of an external admin interface.

### 🔧 Required Functionalities to Implement
These must be added in a separate external system (not inside the main app):

- **External Admin Interface** – Build a standalone admin dashboard for business data management.
- **Business Moderation Tools** – Approve, reject, edit, and feature businesses externally.
- **Analytics Overview** – Display submission counts, approval rates, and user engagement metrics.
- **CSV Export Utility** – Enable CSV export for business listings and newsletter subscribers.
- **Magic Link Authentication** – Secure access for authorized admins using Supabase Auth.

### 📝 Example Reference Code
The repository includes disabled admin component imports that serve only as reference for the new external admin system:
- // Admin components disabled for Apple App Store compliance
- // These are kept as example code for external admin system development
- // import { AdminLogin } from './components/AdminLogin';
- // import { AdminDashboard } from './components/AdminDashboard';

## 🔧 Technical Stack

- **Frontend**: React 18 + TypeScript + Tailwind CSS + Vite
- **Backend**: Supabase (PostgreSQL + Auth + Storage + Edge Functions)
- **Deployment**: Netlify (with automatic builds)
- **Maps**: Apple Maps integration
- **Email**: Supabase Edge Functions (configurable with SendGrid/Resend)

## 📱 iOS App Store Compliance

This web app is designed with iOS App Store guidelines in mind:

- **Touch Targets**: All interactive elements meet 44pt minimum requirement
- **Privacy Ready**: Structured for Privacy Policy compliance
- **Payment Compliance**: Designed to avoid In-App Purchase requirements
- **Sign in with Apple**: Ready for implementation alongside other auth methods
- **Accessibility**: High contrast ratios and screen reader support
- **No Internal Admin Panels**: External data management system required for App Store compliance

## 🚨 Important Notes

### Apple Developer Subscription Compliance
**CRITICAL**: Apple Developer Subscription does not allow internal admin panels within the app. The admin functionality has been disabled to comply with App Store guidelines.

### Data Management Requirements
- **External Admin System**: A separate web application or admin interface must be created for data management
- **Example Code Preserved**: Admin components (`AdminDashboard.tsx`, `AdminLogin.tsx`) are kept as example code for the development team
- **API Integration**: The main app should consume data via API calls rather than direct database access
- **Hard-coded Data**: Current implementation uses hard-coded data that should be replaced with dynamic data from the external admin system

### Admin Code Status
- **Admin Components**: Disabled but preserved as reference code
- **Purpose**: Example implementation for external admin system development
- **Location**: `src/components/AdminDashboard.tsx` and `src/components/AdminLogin.tsx`
- **Note**: These components contain working authentication and CRUD operations that can be adapted for external use

### Legal Documents (Editable)
- **Privacy Policy**: Located in `src/components/PrivacyPolicyPage.tsx` - can be edited directly in the code
- **Terms of Service**: Located in `src/components/TermsOfServicePage.tsx` - can be edited directly in the code
- **Last Updated**: Both documents show "September 2025" and can be updated as needed
- **Content**: Both documents contain placeholder content that should be reviewed and customized for your specific use case

## 🚀 Quick Setup (5 Minutes)

### Prerequisites
- Node.js 18.0.0 or higher
- npm 8.0.0 or higher
- Git
- Supabase account

### Step 1: Clone and Install
```bash
git clone [YOUR_REPOSITORY_URL]
cd rcbmc-directory
npm install
```

### Step 2: Environment Setup
Create a `.env` file in the root directory:
```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
VITE_DEBUG=false
```

### Step 3: Start Development
```bash
npm run dev
```
Your app will be available at `http://localhost:5173`

## 🔧 Detailed Setup Instructions

### Database Setup

#### Option A: Use Existing Database
If you have access to the existing Supabase project, skip to the next section.

#### Option B: Set Up New Database
1. Create a new Supabase project at [supabase.com](https://supabase.com)
2. Go to SQL Editor and run these migrations in order:
   ```sql
   -- Run these migrations in order:
   -- 1. 20250821135904_floral_fog.sql
   -- 2. 20250824030728_wispy_villa.sql
   -- 3. 20250824030733_plain_moon.sql
   -- 4. 20250826024933_calm_garden.sql
   -- 5. 20250904132620_dawn_rice.sql
   -- 6. 20251021093000_create_admin_users.sql
   -- 7. 20250115000000_fix_rls_performance_issues.sql (CRITICAL - Performance Fix)
   ```

#### Configure Row Level Security (RLS)
Create the following policies:

**For `businesses` table:**
```sql
-- Allow public read access to approved businesses
CREATE POLICY "Public can view approved businesses" ON businesses
FOR SELECT USING (status = 'approved');

-- Allow public insert for new business submissions
CREATE POLICY "Public can insert business submissions" ON businesses
FOR INSERT WITH CHECK (true);
```

**For `newsletter_subscribers` table:**
```sql
-- Allow public to subscribe
CREATE POLICY "Public can subscribe" ON newsletter_subscribers
FOR INSERT WITH CHECK (true);
```

### Storage Setup (Optional)
1. Go to Storage in your Supabase dashboard
2. Create a new bucket called `business-logos`
3. Set it to public
4. Configure policies for public read access

## 🚀 Deployment

**For detailed deployment instructions, see [DEPLOYMENT.md](./DEPLOYMENT.md)**

### Quick Deploy Options
1. **Netlify (Recommended)**: Connect GitHub repository for automatic deployment
2. **Vercel**: Use CLI or web interface
3. **Manual Build**: Build locally and upload `dist/` folder

### Required Environment Variables
- `VITE_SUPABASE_URL`: Your Supabase project URL
- `VITE_SUPABASE_ANON_KEY`: Your Supabase anonymous key

## 🔐 Admin Access Setup

### Current Implementation (To Be Modified)
The current admin dashboard is accessible via `Ctrl/Cmd + Shift + A` but must be removed or made external for Apple App Store compliance.

### Recommended External Admin System
Create a separate admin application with:
- Independent authentication system
- Full CRUD operations for businesses
- Analytics and reporting
- CSV export functionality
- API endpoints for the main app

## 🐛 Known Issues and Bugs

**⚠️ IMPORTANT**: This project has several critical issues that must be addressed before production deployment. See [ISSUES.md](./ISSUES.md) for a comprehensive list of all known issues, including:

### Critical Issues (Must Fix)
- **Apple App Store Compliance**: Internal admin dashboard violates Apple Developer Subscription terms
- **Hard-coded Data**: Business data is hard-coded instead of dynamic
- **Supabase RLS Issues**: 12 specific Supabase configuration problems

### Issue Tracking
- **Total Issues**: 19 documented issues
- **Critical**: 2 issues
- **High Priority**: 3 issues  
- **Medium Priority**: 9 issues
- **Low Priority**: 5 issues

**📋 For detailed issue descriptions, causes, impacts, and solutions, see [ISSUES.md](./ISSUES.md)**

## 📊 Code Quality Assessment

### Strengths
- ✅ No linter errors detected
- ✅ Clean code structure with good separation of concerns
- ✅ Proper TypeScript usage in most areas
- ✅ Responsive design implementation
- ✅ Good component organization
- ✅ Proper error boundary implementation
- ✅ Clean git history

### Areas for Improvement
- 🔄 Remove admin dashboard for App Store compliance
- 🔄 Replace hard-coded data with API-driven data
- 🔄 Add comprehensive error handling
- 🔄 Improve TypeScript strictness
- 🔄 Add unit tests
- 🔄 Add integration tests
- 🔄 Add performance monitoring
- 🔄 Add accessibility improvements

## 📄 Legal Documents (Editable)

The project includes easily editable legal documents that can be customized for your specific use case:

### Privacy Policy
- **File**: `src/components/PrivacyPolicyPage.tsx`
- **Current Status**: Contains placeholder content
- **Last Updated**: September 2025 (editable)
- **How to Edit**: Modify the content within the template string in the component
- **Access**: Available via the "More" tab in the app

### Terms of Service
- **File**: `src/components/TermsOfServicePage.tsx`
- **Current Status**: Contains comprehensive placeholder content
- **Last Updated**: September 2025 (editable)
- **How to Edit**: Modify the content within the template string in the component
- **Access**: Available via the "More" tab in the app

### Customization Instructions
1. **Edit Content**: Open the respective `.tsx` files
2. **Update Text**: Modify the content within the template strings
3. **Update Dates**: Change the "Last Updated" dates as needed
4. **Save Changes**: The changes will be reflected immediately in the app
5. **Test**: Verify the changes appear correctly in the app

### Legal Review Recommended
- **Important**: Have legal counsel review both documents before production
- **Customization**: Update contact information, jurisdiction, and specific terms
- **Compliance**: Ensure compliance with local laws and regulations

## 🗂️ Data Models

### Business
```typescript
interface Business {
  id: string;
  business_name: string;
  owner_name: string;
  business_description: string;
  website_url?: string;
  business_category: string;
  certifications: string[];
  logo_url?: string;
  contact_email: string;
  contact_phone?: string;
  business_address: string;
  social_media: object;
  status: 'pending' | 'approved' | 'rejected';
  is_featured: boolean;
  created_at: string;
  updated_at: string;
}
```

### Newsletter Subscriber
```typescript
interface NewsletterSubscriber {
  id: string;
  email: string;
  status: 'active' | 'unsubscribed';
  subscribed_at: string;
  unsubscribed_at?: string;
}
```

## 🎨 Design System

### Colors
- **Primary**: Red (#dc2626, #b91c1c)
- **Secondary**: Black (#000000)
- **Accent**: White (#ffffff)
- **Success**: Green (#16a34a)
- **Warning**: Yellow (#eab308)
- **Error**: Red (#dc2626)

### Typography
- **Font**: Inter (system fallback)
- **Weights**: 400, 500, 600, 700, 800
- **Line Height**: 150% body, 120% headings

### Spacing
- **System**: 8px base unit
- **Touch Targets**: Minimum 44px (iOS compliance)
- **Breakpoints**: Mobile-first responsive design

## 🔮 Roadmap

### Phase 1 (Current - Beta)
- ✅ Business directory with search/filter
- ✅ Business submission and admin approval
- ✅ Newsletter signup and management
- ✅ Business spotlight feature
- ✅ Apple Maps integration
- ✅ **COMPLETED**: Admin dashboard disabled for App Store compliance (code preserved as example)
- 🔄 **CRITICAL**: Replace hard-coded data with API-driven data

### Phase 2 (Fall 2025)
- 🔄 External admin system for data management
- 🔄 API integration for dynamic content
- 🔄 Advanced event management with RSVPs
- 🔄 Business deals and promotions
- 🔄 Enhanced analytics and reporting
- 🔄 PWA features (offline support, install prompts)

### Phase 3 (2026)
- 📱 Native iOS/Android apps
- 🔄 Advanced search with location radius
- 🔄 Business networking features
- 🔄 Integration with chamber management systems
- 🔄 Multi-language support

## 🧪 Testing

### Current Test Coverage
- **Unit Tests**: Not implemented
- **Integration Tests**: Not implemented
- **E2E Tests**: Not implemented
- **Manual Testing**: Basic functionality tested

### Recommended Testing Strategy
1. **Unit Tests**: Test individual components and functions
2. **Integration Tests**: Test component interactions
3. **E2E Tests**: Test complete user workflows
4. **Performance Tests**: Test loading times and responsiveness
5. **Accessibility Tests**: Test screen reader compatibility

## 🔒 Security Considerations

### Current Security Measures
- ✅ Supabase RLS (Row Level Security) policies
- ✅ Environment variable protection
- ✅ No hardcoded secrets in code
- ✅ Proper authentication flow

### Security Recommendations
- 🔄 Add input sanitization
- 🔄 Implement rate limiting
- 🔄 Add CSRF protection
- 🔄 Regular security audits
- 🔄 Dependency vulnerability scanning

## 📱 Mobile Compatibility

### iOS Compatibility
- ✅ Touch targets meet 44pt minimum requirement
- ✅ Responsive design implemented
- ✅ Apple Maps integration
- ✅ PWA-ready structure

### Android Compatibility
- ✅ Responsive design works on Android
- ✅ Touch interactions work properly
- ⚠️ No Android-specific optimizations

## 🌐 Browser Compatibility

### Supported Browsers
- ✅ Chrome (latest)
- ✅ Firefox (latest)
- ✅ Safari (latest)
- ✅ Edge (latest)

### Potential Issues
- ⚠️ Internet Explorer not supported (by design)
- ⚠️ Older mobile browsers may have limited support

## 🚀 Performance Considerations

### Current Performance
- ✅ Vite build optimization
- ✅ Code splitting implemented
- ✅ Image optimization possible
- ✅ Lazy loading implemented

### Performance Recommendations
- 🔄 Add performance monitoring
- 🔄 Implement image lazy loading
- 🔄 Add service worker for caching
- 🔄 Optimize bundle size

## 📋 Action Items for New Owner

### Immediate (First Week)
1. ✅ **COMPLETED**: Admin dashboard disabled for App Store compliance (code preserved as example)
2. **CRITICAL**: Replace hard-coded data with API-driven data
3. Set up proper environment variables
4. Test all functionality in development
5. Review and understand the codebase structure
6. **NEW**: Review admin example code for external system development

### Short Term (First Month)
1. Create external admin system for data management (use example code as reference)
2. Implement API integration for dynamic content
3. Add comprehensive error handling
4. Implement proper testing suite
5. Add performance monitoring

### Long Term (First Quarter)
1. Add comprehensive test coverage
2. Implement CI/CD pipeline
3. Add security scanning
4. Optimize performance
5. Add accessibility improvements

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 📞 Support & Contact

For technical support or questions:
- **Email**: info@rcblackminoritycc.org
- **Website**: [rcblackminoritycc.org](https://rcblackminoritycc.org)

**Richmond County Black & Minority Chamber of Commerce**  
*Empowering businesses, building community*

## 📚 Additional Resources

### External Documentation
- [React Documentation](https://react.dev/)
- [TypeScript Documentation](https://www.typescriptlang.org/)
- [Tailwind CSS Documentation](https://tailwindcss.com/)
- [Supabase Documentation](https://supabase.com/docs)
- [Vite Documentation](https://vitejs.dev/)

### Project Documentation
- **Deployment Guide**: [DEPLOYMENT.md](./DEPLOYMENT.md)
- **Issue Tracking**: [ISSUES.md](./ISSUES.md)
- **Project Handoff**: [Handoff.md](./Handoff.md)

### Useful Commands
```bash
# Check Node.js version
node --version

# Check npm version
npm --version

# Clear npm cache
npm cache clean --force

# Update dependencies
npm update

# Check for security vulnerabilities
npm audit

# Development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run linter
npm run lint
```

---

*This README serves as the complete documentation for the RCBMC Directory project. Please review all sections carefully before proceeding with development or deployment.*
