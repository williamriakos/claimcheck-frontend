# ClaimCheck Frontend - Changelog

## v2.2 - 2026-01-16

### 🎯 **Major Improvements: Mobile-First UI Overhaul**

Blended best UI patterns from benchmark chatbot wrapper with V2 advanced features.

#### **Simplified Scroll Architecture** (from benchmark)
- **chat-messages:** `flex: 1` + `overflow: auto` - ONLY element that scrolls
- Removed complex overflow rules and conflicting constraints
- Cleaner, more reliable scrolling behavior
- Pattern proven from benchmark implementation

#### **Mobile-First Viewport Sizing** (from benchmark)
- Widget height: `min(90vh, 800px)` - never exceeds viewport height
- Min height: `min(600px, 80vh)` - responsive minimum
- Tablet: `min(90vh, 700px)` with narrower sidebar (280px)
- Mobile: `100dvh` - dynamic viewport for better mobile UX
- No more fixed 800px causing scroll issues on smaller screens

#### **Auto-Growing Textarea** (from benchmark)
```javascript
function autoGrow() {
  input.style.height = 'auto';
  input.style.height = Math.min(input.scrollHeight, 120) + 'px';
}
```
- Textarea grows as user types (max 120px)
- Resets to default after message sent
- Better UX for longer questions
- Mobile-friendly input experience

#### **Layout Improvements**
- Fixed mobile `overflow-y: auto` conflict on `.widget-main`
- Changed to `overflow: hidden` - only chat-messages scrolls
- Updated mobile breakpoint to use `100dvh` (dynamic viewport)
- Better handling of iOS Safari viewport changes

#### **Error Handling Enhanced**
- Added visual styling for error messages (orange border + tint)
- Error titles display prominently in orange
- CTA buttons rendered from backend responses
- Fallback messages for network errors
- British English throughout

### **✅ All V2 Features Preserved**
- Backend router integration
- Jurisdiction/category selection
- Features sidebar
- Advanced state management
- Adaptive questioning
- Construction theme
- Database integration
- User transitions

---

## v2.1 - 2026-01-16

### **Mobile Responsiveness**
- Added comprehensive mobile CSS for screens < 640px
- Integrated Riakos Consulting logo (RC+Logo.png)
- Improved touch targets and spacing
- Full-width CTA buttons on mobile

### **Error Handling**
- Constructive error messages (backend + frontend)
- Contextual messaging for rate limits and query limits
- Encouraging tone with upgrade CTAs
- Created ERROR_HANDLING_GUIDE.md

### **Chat Scrolling**
- Fixed overflow issues
- Added custom scrollbar styling
- Improved scrollToBottom with requestAnimationFrame

---

## v2.0 - 2026-01-12

### **Initial V2 Release**
- Advanced conversation engine
- Backend router (/api/claimcheck/v2/turn)
- Adaptive questioning system
- Discriminating factors analysis
- Jurisdiction and category selection
- Rich UI with construction theme
- Features sidebar
- Trial signup integration

---

## v1.0 - Legacy

### **Basic ClaimCheck**
- Simple Q&A chatbot
- Anonymous fingerprinting
- Query limits (3 free)
- Basic trial signup
- Inline widget HTML

---

**Current Version:** v2.2
**Deployment:** Vercel (cc.riakosconsulting.com)
**Backend:** Railway (claimcheck-service-production-d411.up.railway.app)
**Last Updated:** 2026-01-16
