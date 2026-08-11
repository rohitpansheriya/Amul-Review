# 🍦 Amul Ice Cream — Customer Feedback & Review Routing System

A **zero-dependency, single-file** web application that turns every customer visit into a Google review or a direct manager message — with a smart, tap-to-build review composer built in.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Live Demo Flow](#live-demo-flow)
3. [Features](#features)
4. [Project Structure](#project-structure)
5. [Technology Stack](#technology-stack)
6. [Architecture & Design System](#architecture--design-system)
7. [Configuration Variables](#configuration-variables)
8. [Review Category System](#review-category-system)
9. [AI Generated Review Logic](#ai-generated-review-logic)
10. [Routing Logic](#routing-logic)
11. [Clipboard & Redirect Flow](#clipboard--redirect-flow)
12. [WhatsApp Integration](#whatsapp-integration)
13. [Print Poster View](#print-poster-view)
14. [Customization Guide](#customization-guide)
15. [Deployment](#deployment)

---

## Overview

This app is designed to be displayed on a tablet or phone at the point of sale (POS) — or linked via a QR code on a printed poster. Customers rate their experience with 1–5 stars, and the app **intelligently routes** them:

| Rating | Route | Action |
|--------|-------|--------|
| ⭐⭐⭐⭐ / ⭐⭐⭐⭐⭐ | **Positive** | Build a review with tap-to-build tags → Copy & post on Google Maps |
| ⭐ / ⭐⭐ / ⭐⭐⭐ | **Negative** | Type feedback → Send directly to manager via WhatsApp |

The goal: **capture every 4–5 star experience as a public Google review**, while routing every 1–3 star experience privately to the manager before it becomes a public complaint.

---

## Live Demo Flow

```
Customer arrives at page
        │
        ▼
   [Star Rating]  ──────────────────────────────────────────────┐
        │                                                        │
   4 or 5 stars                                          1, 2, or 3 stars
        │                                                        │
        ▼                                                        ▼
[Positive Route]                                         [Negative Route]
        │                                                        │
  Tap category tags                                  Type what went wrong
  (or use ✨ AI)                                                  │
        │                                                        ▼
  Review builds in                                  [WhatsApp Manager Button]
  textarea                                                       │
        │                                                        ▼
  [Copy & Post on                                 Opens wa.me link with
   Google Maps]                                  pre-filled feedback text
        │
        ▼
  Text copied to clipboard
  + redirect to Google Maps
  review page (popup-safe)
```

---

## Features

### ⭐ Star Rating System
- Interactive 5-star SVG rating with smooth hover preview and click-to-lock animations
- Stars glow gold (`#ffc107`) when active, with drop-shadow effect
- Mouse hover previews rating before committing

### 🏷️ Tap-to-Build Tag System (Positive Route)
- **7 category pills**: 🍦 Ice Cream Taste, ⭐ Quality, 😊 Service, ⚡ Speed, 🧼 Cleanliness, 🏪 Atmosphere, 💰 Value for Money
- Each category holds **21 unique review sentences** (147 total)
- Tap a tag → a random sentence from that category is appended to the textarea
- Tap again → that exact sentence is removed (true toggle)
- Multiple tags can be active simultaneously, building a multi-sentence paragraph
- Manual typing is fully supported alongside tag selections

### ✨ AI Generated Review
- One-tap button that instantly assembles a full review paragraph
- Randomly selects **3–4 distinct categories**, picks one sentence from each, joins them
- **Exclusive override**: clears all standard tag selections before generating
- Toggle off: clears the textarea and resets state
- Switching to a standard tag from AI mode: cleanly exits AI mode first

### 📋 Clipboard + Google Maps Redirect
- Copies the review text using the modern `navigator.clipboard` API
- Falls back to `document.execCommand('copy')` for older browsers
- Redirects to Google Maps via a dynamically created `<a target="_blank">` — bypasses popup blockers
- Toast notification confirms successful copy

### 📱 WhatsApp Manager Redirect (Negative Route)
- Direct link to `wa.me/{number}?text={encoded_message}`
- Pre-fills the WhatsApp message with the customer's typed feedback
- Same popup-safe `<a>` click technique as the Google redirect

### 🖨️ Print Poster Mode
- Completely separate A4 poster layout revealed only when printing
- Large QR code placeholder, step-by-step scan instructions, branding
- Replace the decorative QR placeholder with your actual QR code image

### 📱 Mobile-Responsive
- Max-width 480px card, centered on desktop
- Flex-wrap tag pills reflow naturally on small screens
- Touch-friendly tap targets throughout

---

## Project Structure

```
amul-feedback/
├── index.html      ← The entire application (HTML + CSS + JS)
└── README.md       ← This file
```

> **Single-file architecture**: everything — markup, styles, fonts, and logic — lives in `index.html`. Deployable to any static host or served directly via a file:// URL on a local device.

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Structure | Semantic HTML5 |
| Styling | Vanilla CSS with CSS Custom Properties |
| Logic | Vanilla ES6+ JavaScript |
| Fonts | Google Fonts — Inter (400–800) |
| Icons | Inline SVG (Google G, WhatsApp logo) |
| Dependencies | **None** |

---

## Architecture & Design System

### CSS Variables (`:root`)

All brand tokens are defined at the top of the `<style>` block:

```css
:root {
  --color-primary:    #00458b;   /* Header, selected tags, primary buttons */
  --color-secondary:  #eef5ff;   /* Tag idle bg, textarea bg */
  --color-accent:     #e21b22;   /* Google Maps button, error highlight */
  --color-star:       #ffc107;   /* Star rating color, brand dot */
  --color-whatsapp:   #25D366;   /* WhatsApp button */
  --color-bg:         #f0f4f8;   /* Page background */
  --color-surface:    #ffffff;   /* Card background */
  --color-text:       #1a2340;   /* Primary text */
  --color-text-muted: #6b7a99;   /* Hints, labels, footer */
  --color-border:     #dce6f5;   /* Tag borders, dividers, separator */
}
```

### JavaScript State Variables

```js
let currentRating    = 0;     // 1–5, set on star click
let activeSelections = {};    // { categoryLabel: "locked-in sentence" }
let customText       = '';    // User's manually typed additions
let aiTagActive      = false; // Whether the AI tag is currently selected
```

### Key Functions

| Function | Description |
|----------|-------------|
| `buildStars()` | Renders 5 SVG star buttons with hover/click handlers |
| `handleStarClick(n)` | Sets rating, triggers `showRoute()` |
| `showRoute(rating)` | Toggles positive or negative section |
| `buildCategoryTags()` | Renders AI tag + separator + 7 category tags; resets all state |
| `buildTextarea()` | Rebuilds textarea from `activeSelections` + `customText` |
| `onTextareaInput()` | Keeps `customText` in sync with manual typing |
| `handleTagClick(label, el)` | Toggle standard tag; exits AI mode if active |
| `handleAiTagClick(el)` | Toggle AI review; generates 3–4 sentence paragraph |
| `clearAllTags()` | Resets all JS state and visual tag states |
| `pickRandom(arr)` | Returns a random element from an array |
| `copyAndRedirect()` | Clipboard copy → toast → Google Maps redirect |
| `sendWhatsApp()` | Builds wa.me URL with encoded feedback → popup-safe click |
| `showToast()` | Shows the "Review copied!" toast for 3.2 seconds |

---

## Configuration Variables

Located at the top of the `<script>` block — the only section you need to edit to rebrand:

```js
const CONFIG = {
  googleReviewUrl : 'https://g.page/r/CdsQyMcOP8uaEBM/review',
  whatsappNumber  : '918866390389',  // country code + number, no '+'
  positiveStars   : [4, 5],          // change to [5] for 5-star only, etc.
};
```

### Full Rebrand Checklist

| What | Where |
|------|-------|
| Google Review link | `CONFIG.googleReviewUrl` |
| WhatsApp number | `CONFIG.whatsappNumber` |
| Brand name | Two `<span>` tags in header + print poster HTML |
| Positive threshold | `CONFIG.positiveStars` |
| Header greeting | `.header-greeting` `<p>` element |
| Apology text | `.apology-box` `<div>` element |
| Colors | `:root` CSS variables |
| Page title | `<title>` tag |

---

## Review Category System

```js
const reviewsByCategory = {
  '🍦 Ice Cream Taste'  : [ /* 21 sentences */ ],
  '⭐ Quality'          : [ /* 21 sentences */ ],
  '😊 Service'          : [ /* 21 sentences */ ],
  '⚡ Speed'            : [ /* 21 sentences */ ],
  '🧼 Cleanliness'      : [ /* 21 sentences */ ],
  '🏪 Atmosphere'       : [ /* 21 sentences */ ],
  '💰 Value for Money'  : [ /* 21 sentences */ ],
};
```

### Rebuild Model (How Deselect Works)

Instead of string-splicing sentences out of the textarea (fragile), the system uses **deterministic rebuild**:

1. `activeSelections` stores the exact sentence locked in per category
2. `buildTextarea()` always reconstructs `textarea.value` from scratch
3. Deselecting = `delete activeSelections[label]` + `buildTextarea()` → perfectly accurate every time
4. Manual typing is captured by `onTextareaInput()` into `customText`
5. The final textarea value = `Object.values(activeSelections).join(' ') + ' ' + customText`

### Manual Edit Detection

If the user edits text that was auto-generated by tags, `onTextareaInput()` detects it (`!textarea.value.startsWith(builtText)`) and:
- Promotes the entire textarea content to `customText`
- Auto-deselects all tags to keep state consistent

---

## AI Generated Review Logic

```
User clicks ✨ AI Generated Review
           │
    Is aiTagActive?
    ┌─── YES ───┐         ┌─── NO ───┐
    │                         │
clearAllTags()           clearAllTags()
    │                         │
  (done)             Shuffle all 7 categories
                              │
                    Slice first 3 or 4 (random)
                              │
                    pickRandom() from each chosen category
                              │
                    Join with spaces → insert into textarea
                              │
                    aiTagActive = true
                    Add .selected to AI tag
```

**Switching from AI to a standard tag:**
1. `clearAllTags()` — wipes AI text, deactivates AI tag, resets all state
2. Normal standard tag selection proceeds cleanly

---

## Routing Logic

```js
CONFIG.positiveStars = [4, 5]   // Configurable
```

| Stars | Section shown | State initialised |
|-------|--------------|-------------------|
| 4–5 | `#positiveSection` | `buildCategoryTags()` called, textarea cleared |
| 1–3 | `#negativeSection` | Feedback textarea ready for input |

---

## Clipboard & Redirect Flow

Browsers block `window.open()` inside Promise callbacks (like `clipboard.writeText().then(...)`). The workaround:

```js
// Called after clipboard write succeeds
function showToastAndRedirect() {
  showToast();
  // Creates <a target="_blank"> and clicks it — treated as user gesture
  setTimeout(() => {
    const a = document.createElement('a');
    a.href   = CONFIG.googleReviewUrl;
    a.target = '_blank';
    a.rel    = 'noopener noreferrer';
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
  }, 600);
}
```

The 600ms delay also gives the user time to see the toast before the new tab opens.

---

## WhatsApp Integration

```js
const url = `https://wa.me/${CONFIG.whatsappNumber}?text=${encodeURIComponent(fullMsg)}`;
```

Pre-filled message format:
```
Hello, I visited your Amul parlor and would like to share my feedback.

My feedback: [customer's typed text]
```

- Opens WhatsApp app on mobile, WhatsApp Web on desktop
- Uses the same popup-safe `<a>` click technique

---

## Print Poster View

Triggered by `Ctrl+P` / `Cmd+P`.

The `@media print` block hides the web app and shows an A4 poster with:
- Brand pill header
- Large headline + subtitle
- QR code placeholder (replace with real QR)
- Step-by-step scan instructions (1–2–3)
- 5-star decorative row
- Branded footer

### Replacing the QR Code Placeholder

Inside `<div class="poster-qr">`, replace the inner grid with your real QR image:

```html
<img
  src="your-actual-qr-code.png"
  alt="Scan to rate us on Google"
  style="width:60mm; height:60mm; object-fit:contain;"
/>
```

---

## Customization Guide

### Add a New Review Category

```js
// Add to reviewsByCategory:
'🎵 Music & Vibe': [
  'The background music made the visit even more enjoyable.',
  'Great playlist that matched the overall vibe perfectly.',
  // add as many sentences as you like
],
```

The tag renders automatically — no HTML or additional JS needed.

### Change the Positive Star Threshold

```js
// 5-star only:
positiveStars: [5]

// 3-star and above:
positiveStars: [3, 4, 5]
```

### Change AI Sentence Count

In `handleAiTagClick()`:

```js
// Currently: 3 or 4 sentences
const count = Math.floor(Math.random() * 2) + 3;

// Always 4:
const count = 4;

// 2 to 5:
const count = Math.floor(Math.random() * 4) + 2;
```

### Add Translations / Multiple Languages

Replace any sentence in `reviewsByCategory` with a sentence in any language. The app renders plain Unicode — Gujarati, Hindi, Tamil, Arabic, etc. all work natively.

---

## Deployment

| Platform | Method |
|----------|--------|
| **GitHub Pages** | Push `index.html` to repo root, enable Pages in Settings |
| **Netlify Drop** | Drag folder to [app.netlify.com/drop](https://app.netlify.com/drop) |
| **Vercel** | `vercel --prod` from the project folder |
| **Firebase Hosting** | `firebase deploy` with `index.html` as the public root |
| **Local device (tablet/phone)** | Open `index.html` in a browser via file:// URL |
| **QR code** | Host on any HTTPS URL, generate a QR pointing to it |

> **Tip**: Host on **HTTPS** (not file://) to enable the modern `navigator.clipboard` API for reliable clipboard copy. Netlify and GitHub Pages are both free and provide HTTPS automatically.

---

*Built for Amul Ice Cream parlor operations. All brand assets remain property of their respective owners.*
