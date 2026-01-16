# ClaimCheck Error Handling - How It Works

## 🎯 **Architecture Overview**

```
Frontend (Vercel)          Backend (Railway)          User
     ↓                          ↓                      ↓
User Action                 Validation             Sees Message
     ↓                          ↓                      ↓
API Request    ────────>   Processing
                               ↓
                          [SUCCESS?]
                               ↓
                            [ERROR]
                               ↓
                     Generate Error Response
                          (JSON with message)
                               ↓
Frontend Receives  <──────  Error JSON
     ↓
Extract message
     ↓
Display to User   ────────────────────────────────> Constructive
in Chat                                              Error Message
```

---

## 🔄 **Error Flow: Step-by-Step**

### **Step 1: User Action (Frontend)**
```javascript
// User sends a message
sendChatMessage() {
  const answer = input.value.trim();

  // Show user's message immediately
  appendChatMessage({
    role: 'user',
    content: answer
  });

  // Send to backend
  fetch(API_BASE_URL + '/claimcheck/v2/turn', {...})
}
```

### **Step 2: Backend Validation (Railway)**
```javascript
// Backend checks for errors FIRST
const USE_BACKEND_ROUTER = process.env.USE_BACKEND_ROUTER === 'true';

if (!USE_BACKEND_ROUTER) {
  return res.status(200).json({
    success: false,
    message: "We're currently updating our conversation system...",
    friendly_title: "Feature Update in Progress"
  });
}
```

### **Step 3: Backend Returns Error (Railway)**
```javascript
// Rate limit example
if (rateLimitExceeded) {
  return res.status(429).json({
    success: false,
    error: "Rate limit exceeded",
    message: "Thank you for using ClaimCheck! You're using the free version...",
    friendly_title: "Taking a Quick Breather",
    cta: {
      label: "Start 14-Day Free Trial",
      url: "https://riakosconsulting.com/trial",
      description: "Unlimited questions..."
    }
  });
}
```

### **Step 4: Frontend Receives Response (Vercel)**
```javascript
const data = await res.json();

if (data.success) {
  // Success case - show bot message
  appendChatMessage({
    role: 'system',
    content: data.botMessage
  });
} else {
  // ERROR CASE - Backend provided error message
  let errorMessage = data.message || 'We encountered an issue...';

  // Add CTA if backend provided one
  if (data.cta) {
    errorMessage += `\n\n${data.cta.description}\n[${data.cta.label}](${data.cta.url})`;
  }

  appendChatMessage({
    role: 'system',
    content: errorMessage,
    isError: true,
    errorTitle: data.friendly_title || 'Service Notice'
  });
}
```

### **Step 5: Frontend Displays (User Sees)**
```javascript
function renderChatMessage(message) {
  // Error messages get special styling
  const errorTitle = message.errorTitle
    ? `<div style="font-weight: 600; color: var(--construction-orange);">
         ${message.errorTitle}
       </div>`
    : '';

  const rowClass = message.isError
    ? 'chat-row system error-message'  // Orange highlight
    : `chat-row ${message.role}`;

  return `
    <div class="${rowClass}">
      <div class="chat-content">
        ${errorTitle}
        ${content}
      </div>
    </div>
  `;
}
```

---

## 📋 **Error Types & Messages**

### **1. Rate Limit Exceeded (429)**

**When:** User sends too many requests in 15 minutes

**Backend Returns:**
```json
{
  "success": false,
  "error": "Rate limit exceeded",
  "message": "Thank you for using ClaimCheck! You're using the free version and have reached your request limit for this period. To continue your conversation without interruption, please consider upgrading to our trial plan.",
  "friendly_title": "Taking a Quick Breather",
  "details": "Free users are limited to help us maintain service quality...",
  "retry_after_seconds": 900,
  "cta": {
    "label": "Start 14-Day Free Trial",
    "url": "https://riakosconsulting.com/trial",
    "description": "Unlimited questions, faster responses, and access to advanced features"
  }
}
```

**User Sees:**
```
[Taking a Quick Breather]
Thank you for using ClaimCheck! You're using the free version
and have reached your request limit for this period. To continue
your conversation without interruption, please consider upgrading
to our trial plan.

Unlimited questions, faster responses, and access to advanced features
[Start 14-Day Free Trial](link)
```

---

### **2. Query Limit Reached**

**When:** Free user has used all 3 questions

**Backend Returns:**
```json
{
  "success": false,
  "error": "Query limit reached",
  "message": "Thank you for using ClaimCheck! You're currently on our free plan and have used all 3 of your complimentary questions. We're delighted you're finding value in the service.\n\nTo continue exploring your construction payment rights with unlimited questions and access to advanced features, please upgrade to our 14-day free trial—no credit card required.",
  "friendly_title": "Free Questions Used",
  "queries_used": 3,
  "queries_remaining": 0,
  "tier": "free",
  "cta": {
    "label": "Start 14-Day Free Trial",
    "url": "https://riakosconsulting.com/trial",
    "description": "Unlimited questions, detailed analysis, document review, and full platform access"
  }
}
```

**User Sees:**
```
[Free Questions Used]
Thank you for using ClaimCheck! You're currently on our free plan
and have used all 3 of your complimentary questions. We're delighted
you're finding value in the service.

To continue exploring your construction payment rights with unlimited
questions and access to advanced features, please upgrade to our
14-day free trial—no credit card required.

Unlimited questions, detailed analysis, document review, and full platform access
[Start 14-Day Free Trial](link)
```

---

### **3. Backend Router Disabled**

**When:** `USE_BACKEND_ROUTER` environment variable not set

**Backend Returns:**
```json
{
  "success": false,
  "code": "ROUTER_DISABLED",
  "error": "Service temporarily unavailable",
  "message": "We're currently updating our conversation system to serve you better. This feature will be available shortly. Please check back in a few minutes or try our other ClaimCheck options.",
  "friendly_title": "Feature Update in Progress"
}
```

**User Sees:**
```
[Feature Update in Progress]
We're currently updating our conversation system to serve you better.
This feature will be available shortly. Please check back in a few
minutes or try our other ClaimCheck options.
```

---

### **4. Network Error (Frontend Fallback)**

**When:** Cannot reach backend (no internet, server down, CORS issue)

**Frontend Shows:**
```javascript
appendChatMessage({
  role: 'system',
  content: "We're having trouble connecting to our servers. Please check your internet connection and try again. If the problem persists, our service may be temporarily unavailable—we apologise for any inconvenience.",
  isError: true,
  errorTitle: 'Connection Issue'
});
```

**User Sees:**
```
[Connection Issue]
We're having trouble connecting to our servers. Please check your
internet connection and try again. If the problem persists, our
service may be temporarily unavailable—we apologise for any inconvenience.
```

---

### **5. Server Error (500)**

**When:** Backend crashes or database errors

**Backend Returns:**
```json
{
  "success": false,
  "error": "Unable to start session",
  "message": "We're experiencing a brief technical issue on our end. This is temporary and we're working to resolve it. Please try refreshing the page in a moment.",
  "friendly_title": "Connection Issue",
  "technical_details": "Database connection timeout" // Not shown to user
}
```

**User Sees:**
```
[Connection Issue]
We're experiencing a brief technical issue on our end. This is
temporary and we're working to resolve it. Please try refreshing
the page in a moment.
```

---

## 🎨 **Visual Presentation**

### **Error Message Styling:**

```css
.chat-row.error-message {
  background: rgba(204, 102, 0, 0.08);  /* Subtle orange tint */
  border-left: 3px solid var(--construction-orange);  /* Orange accent bar */
}
```

**Result:**
- Orange left border draws attention
- Subtle background tint (not alarming)
- Maintains professional appearance
- Clearly distinct from normal messages

---

## 📊 **Message Structure**

### **Backend Error Response Format:**

```javascript
{
  // Always present
  "success": false,
  "error": "Technical error name",        // For logging/debugging
  "message": "User-friendly explanation", // PRIMARY - shown to user

  // Optional but recommended
  "friendly_title": "Display Title",      // Shows above message
  "details": "Additional context",        // Extra information
  "technical_details": "Error stack",     // Developer info, not shown to user

  // For upgrades/actions
  "cta": {
    "label": "Button Text",
    "url": "https://...",
    "description": "Benefit description"
  },

  // Context-specific fields
  "queries_used": 3,
  "queries_remaining": 0,
  "tier": "free",
  "retry_after_seconds": 900
}
```

### **Frontend Message Object:**

```javascript
{
  id: 'msg-123456',
  timestamp: new Date(),
  role: 'system',
  content: 'User-friendly message from backend',
  isError: true,                    // Triggers error styling
  errorTitle: 'Friendly Title',     // Shows in orange above message
  isLoading: false
}
```

---

## 🔧 **Key Principles**

### **Backend Messages (Primary):**
✅ **Contextual** - Know user's tier, query count, etc.
✅ **Data-driven** - Have access to usage stats
✅ **Consistent** - All errors use same structure
✅ **Actionable** - Include CTAs with URLs
✅ **British English** - "whilst", "apologise", etc.

### **Frontend Messages (Fallback):**
✅ **Helpful** - Guide user to resolution
✅ **Specific** - Network vs parse vs timeout
✅ **Non-blaming** - Never blame user
✅ **Recovery-focused** - How to fix it

---

## 🧪 **Error Hierarchy**

**Priority 1 (Backend Handles):**
1. Rate limiting
2. Query limits
3. Authentication failures
4. Validation errors
5. Business logic errors
6. Database errors

**Priority 2 (Frontend Handles):**
1. Network failures (fetch errors)
2. Timeout errors
3. JSON parse errors
4. Unexpected responses
5. Missing backend errors

---

## 📝 **Message Tone Guidelines**

### **✅ DO:**
- Thank users for using the service
- Acknowledge their tier ("free version", "trial plan")
- Explain what happened and why
- Offer clear next steps
- Highlight benefits of upgrading
- Use positive framing ("We're delighted...")
- Be professional and encouraging

### **❌ DON'T:**
- Apologise excessively ("Sorry, sorry, sorry")
- Use technical jargon (500 error, timeout, etc.)
- Blame the user
- Be vague ("something went wrong")
- Sound defensive or negative
- Leave users without guidance

---

## 🔄 **Example: Complete Error Flow**

### **Scenario: User Hits Rate Limit**

**1. User Action:**
- Sends 11th message in 15 minutes

**2. Frontend:**
```javascript
// Sends request
fetch('/claimcheck/v2/turn', {
  body: JSON.stringify({
    session_id: 'v2_123',
    userMessage: 'What about variations?'
  })
})
```

**3. Backend (Railway):**
```javascript
// Rate limiter middleware intercepts
anonymousRateLimiter.handler() {
  return res.status(429).json({
    success: false,
    message: "Thank you for using ClaimCheck! You're using the free version...",
    friendly_title: "Taking a Quick Breather",
    cta: { ... }
  });
}
```

**4. Frontend Receives:**
```javascript
const data = await res.json();
// data.success === false
// data.message === "Thank you for using ClaimCheck!..."
// data.friendly_title === "Taking a Quick Breather"
// data.cta === { label: "...", url: "..." }
```

**5. Frontend Displays:**
```javascript
appendChatMessage({
  role: 'system',
  content: data.message + '\n\n' + data.cta.description,
  isError: true,
  errorTitle: data.friendly_title
});
```

**6. User Sees in Chat:**
```
┌─────────────────────────────────────────┐
│ [11:23] ▪ User                          │
│ What about variations?                  │
├─────────────────────────────────────────┤
│ [11:23] ▫ System                        │
│                                         │
│ [Taking a Quick Breather]               │ <- Orange title
│ Thank you for using ClaimCheck!         │ <- Constructive
│ You're using the free version and       │    message
│ have reached your request limit...      │
│                                         │
│ [Start 14-Day Free Trial]               │ <- CTA button
└─────────────────────────────────────────┘
   ↑ Orange left border
```

---

## 📊 **Error Message Components**

### **Backend Constructs:**
```javascript
{
  // REQUIRED
  success: false,               // Triggers error path
  error: "Technical name",      // For logs
  message: "User message",      // PRIMARY - what user sees

  // RECOMMENDED
  friendly_title: "Display Title",  // Shows above message

  // OPTIONAL
  details: "Extra info",        // Additional context
  cta: {                        // Call to action
    label: "Button text",
    url: "Link URL",
    description: "Why click?"
  },

  // CONTEXT
  tier: "free",                 // User's current tier
  queries_used: 3,              // Usage stats
  queries_remaining: 0,
  retry_after_seconds: 900      // When can they retry
}
```

### **Frontend Displays:**
```javascript
appendChatMessage({
  role: 'system',              // System message
  content: data.message,       // Backend's message
  isError: true,               // Triggers error styling
  errorTitle: data.friendly_title  // Orange title bar
});
```

**Renders as:**
```html
<div class="chat-row system error-message">
  <div class="chat-content">
    <div style="font-weight: 600; color: #CC6600;">
      [Error Title]
    </div>
    [Constructive error message...]
  </div>
</div>
```

**CSS:**
```css
.chat-row.error-message {
  background: rgba(204, 102, 0, 0.08);    /* Orange tint */
  border-left: 3px solid #CC6600;         /* Orange bar */
}
```

---

## 🎯 **Why This Approach Works**

### **1. Backend Knows Context**
- Has access to database (tier, usage, etc.)
- Knows exact reason for error
- Can provide specific guidance
- Includes actionable CTAs

### **2. Frontend Handles Gracefully**
- Always displays something helpful
- Network errors get friendly messages
- Parse errors explained clearly
- Never shows raw error stack to user

### **3. Consistent User Experience**
- All errors look similar (orange border, title)
- All errors are constructive (not blaming)
- All errors offer next steps
- British English throughout

### **4. Developer Friendly**
- Console logs technical details
- `technical_details` field for debugging
- Error codes for tracking
- Clear separation of concerns

---

## 📱 **Mobile Considerations**

**Error messages are:**
- ✅ Readable on small screens
- ✅ Not cut off by viewport
- ✅ Scrollable if long
- ✅ Touch-friendly CTA buttons

---

## 🧪 **Testing Error Messages**

### **Test Each Error Type:**

**Rate Limit:**
```bash
# Send 51 requests in 15 minutes
for i in {1..51}; do
  curl -X POST https://claimcheck-service.../api/claimcheck/v2/turn \
    -H "Content-Type: application/json" \
    -d '{"session_id":"test","userMessage":"test"}';
done
```

**Query Limit:**
```bash
# Use same fingerprint for 4 questions
# 4th should show "Free Questions Used" message
```

**Network Error:**
```bash
# Disable WiFi and try to send message
# Should see "Connection Issue" message
```

**Backend Down:**
```bash
# Stop Railway service
# Try to use widget
# Should see session start error
```

---

## 🔐 **Security Considerations**

### **What Users See:**
- Friendly error message
- Guidance for resolution
- CTA to upgrade/retry

### **What Users DON'T See:**
- Stack traces
- Database errors
- File paths
- Server internals
- API keys/tokens

### **What Developers See (Console):**
```javascript
console.error('[ClaimCheck Router] Error:', data.error, data.code);
// Logs technical details for debugging
// Users don't see console
```

---

## 📈 **Analytics Opportunities**

**Track Error Occurrences:**
- Rate limit hits → Consider increasing limits
- Query limit hits → Conversion opportunity
- Network errors → Infrastructure issues
- Server errors → Code bugs to fix

**Example:**
```javascript
// In error handler
if (data.code === 'RATE_LIMIT') {
  analytics.track('rate_limit_hit', {
    tier: 'free',
    queries_used: data.queries_used
  });
}
```

---

## ✅ **Implementation Checklist**

### Backend (Railway):
- [x] Rate limiter returns constructive message
- [x] Query limit returns constructive message
- [x] Router disabled returns constructive message
- [x] Session errors return constructive message
- [x] All errors include `friendly_title`
- [x] All errors include actionable `message`
- [x] CTAs included where appropriate
- [x] British English used throughout

### Frontend (Vercel):
- [x] Error messages extracted from backend response
- [x] Fallback messages for network errors
- [x] Error styling (orange border, title)
- [x] CTA buttons rendered from backend data
- [x] Console logging for debugging
- [x] isError flag triggers special styling
- [x] errorTitle displayed prominently

---

## 🎓 **Summary**

**Backend = Smart (Knows Context)**
- Has user data, tier info, usage stats
- Provides constructive, personalised messages
- Includes CTAs and next steps

**Frontend = Display Layer**
- Shows backend messages as-is
- Adds styling and formatting
- Handles network failures gracefully
- Never blocks user with generic errors

**Result = Better UX**
- Users always know what happened
- Users always know what to do next
- Errors become conversion opportunities
- Professional, helpful, encouraging

---

**Last Updated:** 2026-01-16
**Applies To:** ClaimCheck V2 Widget
**Architecture:** Frontend (Vercel) + Backend (Railway)
