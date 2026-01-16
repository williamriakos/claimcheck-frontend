# ClaimCheck Frontend

Frontend application for ClaimCheck - Construction payment claim pre-screening service.

## 🎯 Architecture

```
Vercel (Frontend)              Railway (Backend API)
cc.riakosconsulting.com  →  claimcheck-service-production-d411.up.railway.app
├── Landing Page (/)           ├── API: /api/claimcheck/*
└── Widget (/claimcheck-service)  └── Shared PostgreSQL Database
```

## 📁 Project Structure

```
claimcheck-frontend/
├── public/
│   ├── index.html              # Landing page
│   └── claimcheck-service.html # Widget application
├── vercel.json                 # Vercel configuration
├── package.json                # Project metadata
└── README.md                   # This file
```

## 🚀 Deployment to Vercel

### Prerequisites

1. Vercel account: https://vercel.com/signup
2. Vercel CLI (optional): `npm i -g vercel`
3. GitHub account (for automatic deployments)

### Option 1: Deploy via GitHub (Recommended)

1. **Create GitHub Repository**
   ```bash
   cd /Users/williamriakos/claimcheck-frontend
   git add .
   git commit -m "Initial commit: ClaimCheck frontend for Vercel"
   gh repo create claimcheck-frontend --public --source=. --remote=origin --push
   ```

2. **Connect to Vercel**
   - Go to: https://vercel.com/dashboard
   - Click: **Add New... → Project**
   - Import: **claimcheck-frontend** repository
   - Framework Preset: **Other** (static site)
   - Root Directory: `./`
   - Click: **Deploy**

3. **Add Custom Domain**
   - Go to: Project Settings → Domains
   - Add: `cc.riakosconsulting.com`
   - Vercel will provide DNS configuration

### Option 2: Deploy via CLI

1. **Install Vercel CLI**
   ```bash
   npm i -g vercel
   ```

2. **Login to Vercel**
   ```bash
   vercel login
   ```

3. **Deploy**
   ```bash
   cd /Users/williamriakos/claimcheck-frontend
   vercel --prod
   ```

4. **Add Custom Domain**
   ```bash
   vercel domains add cc.riakosconsulting.com
   ```

## 🌐 DNS Configuration

### Update Cloudflare DNS

1. Go to: https://dash.cloudflare.com
2. Select: **riakosconsulting.com**
3. Go to: **DNS → Records**
4. Edit the `cc` CNAME record:
   - **Type:** CNAME
   - **Name:** cc
   - **Target:** `cname.vercel-dns.com` (Vercel will provide exact target)
   - **Proxy status:** DNS Only (grey cloud ☁️) - required for Vercel verification
   - **TTL:** Auto

5. After Vercel verifies, you can re-enable proxy (orange cloud ☁️)

## 🧪 Testing

### Local Testing

```bash
cd /Users/williamriakos/claimcheck-frontend
vercel dev
```

Open: http://localhost:3000

### Test Endpoints

**Landing Page:**
- URL: `https://cc.riakosconsulting.com/`
- Expected: ClaimCheck landing page with hero, features, CTA

**Widget:**
- URL: `https://cc.riakosconsulting.com/claimcheck-service`
- Expected: ClaimCheck widget interface

**API Connection:**
- Widget should connect to Railway backend
- Check browser console for API calls to: `claimcheck-service-production-d411.up.railway.app`

## 🔧 Configuration

### Backend API URL

The widget is configured to call the Railway backend:
```javascript
const API_BASE_URL = 'https://claimcheck-service-production-d411.up.railway.app/api';
```

To change the backend URL, edit:
- `public/claimcheck-service.html` (line ~2257)

### Routing

Vercel rewrites configured in `vercel.json`:
- `/` → Landing page
- `/claimcheck-service` → Widget application

## 📊 Environment Variables

This frontend is static and requires no environment variables.

All API configuration is in the HTML files.

## 🔐 CORS Configuration

The Railway backend must allow requests from:
- `https://cc.riakosconsulting.com`

This is already configured in the backend CORS settings.

## 🆘 Troubleshooting

### Issue: "Domain not verified"
- Ensure DNS CNAME points to Vercel target
- Wait 24-48 hours for DNS propagation
- Try disabling Cloudflare proxy temporarily

### Issue: "API calls failing"
- Check Railway backend is running
- Check CORS is configured for `cc.riakosconsulting.com`
- Check browser console for specific errors

### Issue: "404 on /claimcheck-service"
- Verify `vercel.json` rewrites are correct
- Redeploy: `vercel --prod`

## 📈 Monitoring

### Vercel Dashboard
- Deployments: https://vercel.com/dashboard
- Analytics: Built-in Vercel Analytics
- Logs: Real-time function logs

### Railway Backend
- Status: https://railway.app/dashboard
- API health: https://claimcheck-service-production-d411.up.railway.app/api/health

## 🔄 Updates & Redeployment

### Update Frontend

1. Make changes to HTML files in `public/`
2. Commit and push to GitHub:
   ```bash
   git add .
   git commit -m "Update frontend"
   git push
   ```
3. Vercel auto-deploys on push (if connected to GitHub)

### Manual Redeploy

```bash
vercel --prod
```

## ✅ Success Criteria

- [ ] Landing page loads at `cc.riakosconsulting.com`
- [ ] Widget loads at `cc.riakosconsulting.com/claimcheck-service`
- [ ] No Railway domains visible in browser
- [ ] API calls to Railway backend successful
- [ ] HTTPS/SSL working
- [ ] CTA button redirects to `/claimcheck-service`

## 🎉 Post-Deployment

Once deployed:

1. Test full user flow:
   - Visit landing page
   - Click "Start ClaimCheck"
   - Complete widget questions
   - Verify data saved to shared database

2. Update any external links:
   - Squarespace iframes
   - Marketing materials
   - Social media links

3. Monitor:
   - Vercel analytics for traffic
   - Railway logs for API errors
   - User feedback

---

**Deployed:** 2026-01-16
**Frontend:** Vercel
**Backend:** Railway
**Database:** Railway PostgreSQL (shared with Elriax platform)
