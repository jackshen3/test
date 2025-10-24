# Deployment Guide

This guide covers all deployment options for the RCBMC Directory project.

## 🚀 Primary Deployment: Netlify (Recommended)

### Quick Deploy Options

### Option 1: Direct from GitHub
1. Push this code to a GitHub repository
2. Go to [Netlify](https://netlify.com) and sign in
3. Click "New site from Git"
4. Connect your GitHub repository
5. Build settings will be automatically detected from `netlify.toml`

### Option 2: Drag & Drop Deploy
1. Run `npm run build` locally
2. Go to [Netlify](https://netlify.com)
3. Drag the `dist` folder to the deploy area

### Option 3: Netlify CLI
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Login to Netlify
netlify login

# Deploy
netlify deploy --prod --dir=dist
```

## Environment Variables Setup

After deployment, add these environment variables in Netlify:

1. Go to Site settings > Environment variables
2. Add the following variables:
   - `VITE_SUPABASE_URL`: Your Supabase project URL
   - `VITE_SUPABASE_ANON_KEY`: Your Supabase anonymous key

## Build Settings

The `netlify.toml` file configures:
- Build command: `npm run build`
- Publish directory: `dist`
- SPA redirects for React Router
- Node.js version: 18

## Custom Domain (Optional)

1. In Netlify dashboard, go to Domain settings
2. Add your custom domain
3. Configure DNS records as instructed

## 🔄 Alternative Deployment Options

### Vercel
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel

# Follow prompts and add environment variables
```

### Manual Build & Deploy
```bash
# Build the project
npm run build

# Upload the dist/ folder to any static hosting service
# Examples: AWS S3, GitHub Pages, Firebase Hosting, etc.
```

## 🔧 Environment Variables

### Required Variables
- `VITE_SUPABASE_URL`: Your Supabase project URL
- `VITE_SUPABASE_ANON_KEY`: Your Supabase anonymous key
- `VITE_DEBUG`: Set to `false` for production

### Setting Variables
- **Netlify**: Site settings > Environment variables
- **Vercel**: Project settings > Environment variables
- **Manual**: Set in your hosting platform's environment configuration

## 🗄️ Database Setup

Before deploying, ensure your Supabase project is configured:

1. **Run Migrations**: Execute all migration files in order (see [README.md](./README.md#database-setup))
2. **Configure RLS**: Set up Row Level Security policies
3. **Storage Setup**: Create `business-logos` bucket
4. **Edge Functions**: Deploy email notification functions

## 🌐 Post-Deployment

Your site will be live at:
- **Netlify**: `https://your-site-name.netlify.app`
- **Vercel**: `https://your-project.vercel.app`
- **Custom Domain**: Configure in your hosting platform

## 🔍 Troubleshooting

### Common Issues
- **Build Failures**: Check Node.js version (requires 18+)
- **Environment Variables**: Ensure all required variables are set
- **Database Connection**: Verify Supabase URL and keys are correct
- **Routing Issues**: Ensure SPA redirects are configured

### Support
- **Technical Issues**: See [ISSUES.md](./ISSUES.md)
- **Setup Help**: See [README.md](./README.md)
- **Project Handoff**: See [Handoff.md](./Handoff.md)