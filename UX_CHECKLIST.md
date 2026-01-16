# ClaimCheck Widget - UX/UI Quality Checklist

## 🎯 **Critical Scrolling Behavior**

### ✅ **ONLY Chat Messages Area Scrolls**
- [ ] Chat messages container (`.chat-messages`) has `overflow-y: scroll`
- [ ] Chat messages scrolls independently when content exceeds viewport
- [ ] Scrollbar is visible and styled (construction theme colors)
- [ ] Smooth scrolling behavior with `requestAnimationFrame`
- [ ] Auto-scrolls to bottom on new messages

### ❌ **These NEVER Scroll (Must Remain Fixed)**
- [ ] Text input area (`.chat-input-area`) - Fixed at bottom
- [ ] Header/title panel - Fixed at top
- [ ] Features sidebar (`.widget-sidebar`) - Fixed on right
- [ ] HTML/Body elements - `overflow: hidden`
- [ ] Widget container (`.claimcheck-widget`) - `overflow: hidden`

---

## 📱 **Mobile Responsiveness**

### Layout
- [ ] Widget fits viewport on all screen sizes (100vh/100dvh)
- [ ] No horizontal scrolling
- [ ] Touch scrolling works smoothly (`-webkit-overflow-scrolling: touch`)
- [ ] Safe area insets respected (iPhone notch, bottom bar)

### Touch Targets
- [ ] Buttons minimum 44x44px (Apple HIG standard)
- [ ] Input areas easy to tap
- [ ] Sufficient spacing between interactive elements
- [ ] No accidental taps on adjacent elements

### Typography
- [ ] Font sizes readable on mobile (13px+ for body, 11px+ for labels)
- [ ] Line height appropriate (1.4-1.6 for readability)
- [ ] Sufficient contrast ratios (WCAG AA minimum)

---

## 🎨 **Visual Design**

### Color & Branding
- [ ] Construction theme colors consistent (`--construction-orange: #CC6600`)
- [ ] Riakos Consulting logo displays correctly
- [ ] Dark theme throughout (terminal bg: #0d1b0d)
- [ ] Accent colors visible against dark backgrounds

### Visual Hierarchy
- [ ] User messages visually distinct from AI messages
- [ ] Clear separation between message bubbles
- [ ] Loading states clearly visible
- [ ] Action buttons stand out

### Spacing & Layout
- [ ] Consistent padding/margins throughout
- [ ] No elements touching viewport edges (16px minimum)
- [ ] Breathing room around text (not cramped)
- [ ] Grid/flexbox alignment correct

---

## ⚡ **Performance & Loading**

### Initial Load
- [ ] Widget appears quickly (<3 seconds)
- [ ] Loading states shown for async operations
- [ ] No layout shift (CLS) during load
- [ ] Fingerprint initialized before user interaction

### Interactions
- [ ] Message send is instant (no delay)
- [ ] Loading indicators for AI responses
- [ ] Smooth animations (60fps)
- [ ] No janky scrolling

### API Performance
- [ ] Rate limits appropriate (50 requests/15min for free tier)
- [ ] Backend router enabled (`USE_BACKEND_ROUTER=true`)
- [ ] Error handling for failed requests
- [ ] Timeout handling (30 second max)

---

## 🔄 **User Interactions**

### Input Behavior
- [ ] Enter key sends message
- [ ] Shift+Enter for new line
- [ ] Input clears after send
- [ ] Input disabled during loading
- [ ] Placeholder text clear and helpful
- [ ] Character limit enforced (if applicable)

### Message Display
- [ ] Messages appear in correct order
- [ ] Timestamps accurate
- [ ] User/AI attribution clear
- [ ] Long messages wrap correctly
- [ ] Code blocks formatted (if applicable)
- [ ] Links clickable (if applicable)

### Buttons & Actions
- [ ] All buttons have hover states
- [ ] Disabled state visually distinct
- [ ] Click feedback (ripple/color change)
- [ ] No double-submit issues
- [ ] Action buttons contextual to conversation state

---

## 🔐 **Session & Authentication**

### Fingerprinting
- [ ] Fingerprint generated before first API call
- [ ] Fallback if fingerprinting blocked
- [ ] Session ID created successfully
- [ ] Session persists across page refresh (if intended)

### Query Limits
- [ ] Free tier limit displayed (3 queries)
- [ ] Query counter updates correctly
- [ ] Upgrade prompt at limit
- [ ] Trial signup flow works

---

## 🐛 **Error Handling**

### User-Facing Errors
- [ ] Clear error messages (no technical jargon)
- [ ] Retry options for failed requests
- [ ] Helpful suggestions for resolution
- [ ] No silent failures

### Edge Cases
- [ ] Empty input handled gracefully
- [ ] Network offline handled
- [ ] Server errors (500) handled
- [ ] Rate limit errors (429) explained clearly
- [ ] Malformed responses handled

### Console Errors
- [ ] No JavaScript errors in console
- [ ] No 404s for assets
- [ ] No CORS errors
- [ ] Warnings addressed or documented

---

## ♿ **Accessibility**

### Keyboard Navigation
- [ ] Tab order logical
- [ ] Focus indicators visible
- [ ] All interactive elements keyboard accessible
- [ ] Escape key closes modals

### Screen Readers
- [ ] Semantic HTML (proper heading hierarchy)
- [ ] Alt text for images
- [ ] ARIA labels where needed
- [ ] Role attributes on interactive elements

### Visual Accessibility
- [ ] Color not only indicator (use icons/text too)
- [ ] Text contrast passes WCAG AA (4.5:1 minimum)
- [ ] Focus indicators visible (3:1 contrast)
- [ ] No text smaller than 11px

---

## 📊 **Content & Copy**

### Messaging
- [ ] Welcome message clear and inviting
- [ ] Instructions easy to follow
- [ ] British English throughout (colour, organisation, etc.)
- [ ] Professional tone consistent
- [ ] Legal disclaimers present (if required)

### Error Messages
- [ ] Friendly, not blame-focused
- [ ] Actionable guidance provided
- [ ] Technical details hidden (available in console)

---

## 🔄 **State Management**

### Conversation State
- [ ] Current step tracked correctly
- [ ] Selected jurisdiction persisted
- [ ] Category selection saved
- [ ] Adaptive questions tracked
- [ ] User questions counted correctly

### UI State
- [ ] Loading states accurate
- [ ] Disabled states correct
- [ ] Button states reflect conversation phase
- [ ] Counter displays match backend

---

## 🧪 **Testing Scenarios**

### Happy Path
- [ ] Complete conversation from start to finish
- [ ] Switch jurisdictions mid-flow
- [ ] Ask follow-up questions
- [ ] Upgrade to trial

### Stress Tests
- [ ] 50+ message conversation
- [ ] Very long messages (1000+ characters)
- [ ] Rapid-fire messages
- [ ] Multiple browser tabs

### Browser Compatibility
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest, iOS + macOS)
- [ ] Edge (latest)
- [ ] Mobile browsers (Chrome, Safari iOS)

---

## 🚀 **Deployment Checks**

### Vercel Frontend
- [ ] Domain: `cc.riakosconsulting.com`
- [ ] Landing page loads
- [ ] Widget loads at `/claimcheck-service`
- [ ] SSL certificate valid
- [ ] No Railway domains visible in UI

### Railway Backend
- [ ] API responding at Railway domain
- [ ] Environment variables set:
  - [ ] `USE_BACKEND_ROUTER=true`
  - [ ] `CLAIMCHECK_ANONYMOUS_RATE_LIMIT=50` (optional)
  - [ ] Database connection string
  - [ ] JWT secrets
- [ ] CORS allows `cc.riakosconsulting.com`
- [ ] Rate limits appropriate

### Database
- [ ] Sessions created correctly
- [ ] Messages saved to database
- [ ] Query counts updated
- [ ] Shared database with Elriax platform
- [ ] User transitions work

---

## 📈 **Analytics & Monitoring**

### User Behavior
- [ ] Track session starts
- [ ] Track message counts
- [ ] Track query limit hits
- [ ] Track trial signups
- [ ] Track errors/failures

### Performance Monitoring
- [ ] API response times
- [ ] Frontend load times
- [ ] Error rates
- [ ] Rate limit hits

---

## 🔧 **Developer Experience**

### Code Quality
- [ ] No console errors
- [ ] No console warnings (or documented)
- [ ] Code commented where complex
- [ ] Naming conventions consistent

### Documentation
- [ ] README up to date
- [ ] DEPLOY.md accurate
- [ ] API endpoints documented
- [ ] Environment variables listed

---

## ✅ **Final Pre-Launch Checklist**

- [ ] All critical scrolling behavior verified
- [ ] Mobile responsive on real devices
- [ ] Rate limits allow normal conversations
- [ ] Error handling graceful
- [ ] Backend router enabled
- [ ] CORS configured correctly
- [ ] Database connections working
- [ ] User transitions to platform functional
- [ ] No Railway domains visible to users
- [ ] Logo displays correctly
- [ ] British English throughout
- [ ] Legal disclaimers present
- [ ] Analytics tracking active

---

**Last Updated:** 2026-01-16
**Tested By:** ___________
**Deployment:** Production @ cc.riakosconsulting.com
