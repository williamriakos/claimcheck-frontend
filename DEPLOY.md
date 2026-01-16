# 🚀 Quick Deployment Guide

## ✅ What's Ready

Your ClaimCheck frontend is ready to deploy to Vercel!

**Location:** `/Users/williamriakos/claimcheck-frontend`

## 📋 Quick Start (5 minutes)

### Step 1: Push to GitHub

```bash
cd /Users/williamriakos/claimcheck-frontend

# Create GitHub repository
gh repo create claimcheck-frontend --public --source=. --remote=origin --push

# Or manually:
# 1. Create repo at https://github.com/new
# 2. git remote add origin https://github.com/williamriakos/claimcheck-frontend.git
# 3. git push -u origin main
```

### Step 2: Deploy to Vercel

1. **Go to Vercel:** https://vercel.com/new
2. **Import Git Repository:**
   - Select: **claimcheck-frontend**
   - Framework: **Other**
   - Root Directory: `./`
   - Click: **Deploy**

3. **Wait for deployment** (~30 seconds)

### Step 3: Add Custom Domain

1. **In Vercel Dashboard:**
   - Go to: Project Settings → Domains
   - Click: **Add Domain**
   - Enter: `cc.riakosconsulting.com`
   - Copy the DNS target Vercel provides

2. **Update Cloudflare DNS:**
   - Go to: https://dash.cloudflare.com
   - Select: **riakosconsulting.com**
   - DNS → Records → Edit `cc` CNAME
   - Update **Target** to Vercel's target
   - Set **Proxy status**: DNS Only (grey cloud) - required for verification
   - Save

3. **Wait for verification** (2-10 minutes)

4. **Re-enable Cloudflare proxy** (optional, after verification)

### Step 4: Test

Visit: `https://cc.riakosconsulting.com`

**Landing page should load ✅**

Visit: `https://cc.riakosconsulting.com/claimcheck-service`

**Widget should load and connect to Railway backend ✅**

---

## 🎯 What Happens Next

### Current Setup (Before Deployment)
```
cc.riakosconsulting.com → Railway (frontend + backend)
```

### After Vercel Deployment
```
cc.riakosconsulting.com → Vercel (frontend only)
                             ↓
                          Railway backend API
```

### Benefits
- ✅ Faster frontend (Vercel CDN)
- ✅ Cleaner separation (frontend/backend)
- ✅ No Railway domains visible
- ✅ Easier frontend updates

---

## 🔧 Configuration Summary

**Frontend (Vercel):**
- Landing page: `/`
- Widget: `/claimcheck-service`
- Repository: `claimcheck-frontend`

**Backend (Railway):**
- API: `claimcheck-service-production-d411.up.railway.app`
- Endpoints: `/api/claimcheck/*`
- Already configured with CORS for `cc.riakosconsulting.com`

**Database:**
- Shared PostgreSQL on Railway
- Enables seamless user transitions to platform.riakosconsulting.com

---

## ⚠️ Important Notes

1. **After Vercel deployment**, users visiting `cc.riakosconsulting.com` will see the Vercel-hosted frontend
2. **Railway backend** continues running - just serving API, not frontend
3. **Shared database** means user data flows between ClaimCheck and Elriax platform
4. **No code changes needed** - frontend already configured to call Railway API

---

## 📞 Support

**Documentation:** See README.md for full details

**Troubleshooting:** See README.md → Troubleshooting section

**Questions:** Open an issue in the GitHub repository

---

**Ready to deploy!** Follow Steps 1-4 above. 🚀
