# 🍦 Amul Ice Cream Parlor — Customer Feedback & Review Routing System

A **single-file, zero-dependency** web application that intelligently routes customer feedback based on their star rating. Happy customers (4–5 stars) are guided to leave a public Google review; unhappy customers (1–3 stars) are routed privately to WhatsApp so issues can be resolved internally.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [File Structure](#file-structure)
- [How It Works — User Flow](#how-it-works--user-flow)
- [Configuration](#configuration)
- [Customisation Guide](#customisation-guide)
- [Print Poster / QR Code Setup](#print-poster--qr-code-setup)
- [Review Category Data](#review-category-data)
- [AI Generated Review Feature](#ai-generated-review-feature)
- [Technology Stack](#technology-stack)
- [Browser Compatibility](#browser-compatibility)
- [Deployment](#deployment)
- [Brand / CSS Variables Reference](#brand--css-variables-reference)

---

## Overview

This app was built for an **Amul Ice Cream Parlour** to increase the volume and quality of Google reviews while privately capturing negative feedback before it reaches public platforms.

The entire application lives in a **single `index.html` file** — no build tools, no frameworks, no npm. Drop the file on any static host and it's live.

---

## Features

### ⭐ Star Rating System
- Interactive 5-star rating widget with smooth hover and click animations
- Animated star fill with SVG icons
- A pop keyframe animation on click for tactile feedback
- Descriptive label updates dynamically (e.g., *"😊 4 Stars — Good"*)

### 🔀 Smart Routing Logic

| Rating | Route | Action |
|--------|-------|--------|
| ⭐⭐⭐⭐ / ⭐⭐⭐⭐⭐ | **Positive** | Opens "Build Your Review" section |
| ⭐ / ⭐⭐ / ⭐⭐⭐ | **Negative** | Opens "Share Your Feedback" section |

### 🏷️ Tap-to-Build Tag System (Positive Route)
- **7 category pills**: Ice Cream Taste, Quality, Service, Speed, Cleanliness, Atmosphere, Value for Money
- Each pill is a **toggle**: tap once to add a sentence, tap again (shown in red) to remove it
- Multiple pills can be active simultaneously — sentences are appended in order
- Textarea is rebuilt from scratch on every toggle — no stale text
- **Manual typing** is preserved as `customText` and survives tag toggles
- Character counter (max 1000)

### ✨ AI Generated Review
- A special gradient purple pill that auto-generates a full review paragraph
- Picks **3 or 4 distinct random categories** (never repeats a category)
- Selects one sentence per category and combines them into a cohesive paragraph
- **Mutually exclusive** with standard tags — activating AI clears all standard pills and vice versa
- Toggle off to clear; the spinning ✨ icon indicates active state

### 📋 Copy & Redirect (Positive Route)
- "Copy & Open Google Maps" button copies the review text to clipboard
- Immediately opens the Google Maps review URL in a new tab
- Safe redirect method bypasses browser popup blockers

### 💬 WhatsApp Feedback (Negative Route)
- Free-text textarea for private feedback
- "Send via WhatsApp" button composes a pre-filled WhatsApp message including the star rating
- Opens WhatsApp Web/app via `wa.me` deep link

### 🔔 Toast Notifications
- Non-intrusive slide-up toast for copy confirmation and validation errors
- Auto-dismisses after ~3 seconds

### 🖨️ A4 Print Poster
- Hidden in browser view; visible only when printing (`@media print`)
- Displays a large QR code placeholder, title, and step-by-step scan instructions
- Ready for a real QR code image to be swapped in

### 📱 Mobile-Responsive
- Max-width 480px centered card layout
- Fluid pill wrapping, touch-friendly tap targets
- Tested at 320px minimum width

---

## File Structure

```
amul-review-app/
│
├── index.html          ← The entire application (HTML + CSS + JS)
└── README.md           ← This file
```

Everything is self-contained. There are no external JS libraries. Google Fonts (`Inter` + `Outfit`) are loaded via a `<link>` tag — the app degrades gracefully to system fonts if offline.

---

## How It Works — User Flow

```
User opens page
      │
      ▼
  Taps a star (1–5)
      │
      ├─── 4 or 5 Stars ──────────────────────────────────┐
      │                                                    │
      │    "Build Your Review" section appears             │
      │         │                                          │
      │         ├── Tap category pills to add sentences    │
      │         ├── Tap ✨ AI Generated Review for auto    │
      │         ├── Edit textarea freely                   │
      │         └── "Copy & Open Google Maps" →            │
      │               copies text + opens Google Review    │
      │                                                    │
      └─── 1, 2, or 3 Stars ──────────────────────────────┘
                                                           │
           "Share Your Feedback" section appears           │
                │                                          │
                ├── Optional free-text feedback            │
                └── "Send via WhatsApp" →                  │
                      opens WhatsApp with pre-filled msg   │
```

---

## Configuration

All business-specific values are in a single `CONFIG` object near the top of the `<script>` section:

```javascript
const CONFIG = {
  googleReviewLink: 'https://g.page/r/CdsQyMcOP8uaEBM/review',  // ← your Google review URL
  whatsappNumber:   '918866390389',                              // ← country code + number (no +)
  brandName:        'Amul Ice Cream',                            // ← used in WhatsApp message
};
```

### How to find your Google Review link
1. Open [Google Business Profile](https://business.google.com)
2. Go to your location → **Get more reviews**
3. Copy the short link — it looks like `https://g.page/r/XXXXXXXXXXXX/review`

### WhatsApp number format
- Include the country code without the `+` prefix
- Example: India number `+91 88663 90389` → `'918866390389'`

---

## Customisation Guide

### Changing Brand Colors
All colors are defined as CSS custom properties in `:root`:

```css
:root {
  --color-primary:    #00458b;   /* Amul blue — buttons, active pills */
  --color-accent:     #e21b22;   /* Amul red — deselect hover, shadows */
  --color-star:       #ffc107;   /* Gold — star fill, pulsing dot */
  --color-whatsapp:   #25D366;   /* WhatsApp green */
  --color-secondary:  #eef5ff;   /* Light blue — card backgrounds */
  --color-bg:         #f0f4fa;   /* Page background */
  --color-text-dark:  #1a2340;
  --color-text-mid:   #445070;
  --color-text-light: #7a8aaa;
}
```

Change `--color-primary` and `--color-accent` to instantly re-theme the entire app for any brand.

### Changing the Brand Name in the Badge
Find the HTML near the top of `<body>`:

```html
<span class="brand-badge-text">Amul Ice Cream</span>
```

Replace `Amul Ice Cream` with your parlour name.

### Changing Fonts
The app uses two Google Fonts:
- **`Outfit`** — headings (`--font-head`)
- **`Inter`** — body text (`--font-body`)

Replace the `<link>` tag in `<head>` with any other Google Fonts pair and update the two CSS variables.

---

## Print Poster / QR Code Setup

The app includes a hidden A4 poster that only appears when the browser's print function is triggered (`Ctrl+P` / `Cmd+P`).

### To add a real QR code
1. Generate a QR code pointing to your Google Review link (use [qr.io](https://qr.io) or [goqr.me](https://goqr.me))
2. Download as PNG (minimum 600×600 px recommended for print quality)
3. In `index.html`, find the `#print-poster` section and replace the placeholder div with an `<img>` tag:

```html
<!-- Before -->
<div class="poster-qr-placeholder">
  <span class="poster-qr-placeholder-text">Place Your<br>QR Code Here</span>
</div>

<!-- After -->
<img src="your-qr-code.png" alt="Scan to review" style="width:220px;height:220px;" />
```

### Printing the poster
Open `index.html` in Chrome → `File → Print` → set paper size to **A4**, orientation **Portrait** → Print or Save as PDF.

---

## Review Category Data

The `REVIEWS_BY_CATEGORY` object contains **7 categories × 21 sentences = 147 total review sentences** in English.

| Category | Emoji | Sentences |
|----------|-------|-----------|
| Ice Cream Taste | 🍦 | 21 |
| Quality | ⭐ | 21 |
| Service | 😊 | 21 |
| Speed | ⚡ | 21 |
| Cleanliness | 🧼 | 21 |
| Atmosphere | 🏪 | 21 |
| Value for Money | 💰 | 21 |

### Adding a new category
In the `REVIEWS_BY_CATEGORY` object, add a new key–value pair:

```javascript
'🌟 New Category': [
  'First review sentence for this category.',
  'Second review sentence.',
  // add as many sentences as you like
],
```

A new pill will automatically appear — no other code changes required.

### Editing existing sentences
Find `REVIEWS_BY_CATEGORY` in the `<script>` section and edit any string in any array.

---

## AI Generated Review Feature

The `✨ AI Generated Review` pill generates a full review paragraph by:

1. **Shuffling** all 7 category keys randomly using `Array.sort(() => Math.random() - 0.5)`
2. **Picking 3 or 4** (50/50 probability) distinct categories from the shuffled order
3. **Selecting one sentence** at random from each chosen category
4. **Joining** the sentences with a space into a single cohesive paragraph

### Mutual exclusivity rules

| Action | Result |
|--------|--------|
| Click AI pill (inactive) | Clears all standard pills → inserts AI text → AI pill active (spinning ✨) |
| Click AI pill (active) | Full reset — everything cleared |
| Click any standard pill while AI active | AI deactivates → textarea cleared → standard logic applies |
| Click "Clear" button | Full reset — all pills off, textarea empty |

---

## Technology Stack

| Layer | Technology | Notes |
|-------|------------|-------|
| Structure | HTML5 | Semantic elements, ARIA attributes throughout |
| Styling | Vanilla CSS | CSS custom properties, flexbox, `@keyframes`, `@media print` |
| Logic | Vanilla JavaScript (ES2020) | No frameworks, no bundler |
| Fonts | Google Fonts CDN | Inter + Outfit |
| Clipboard | `navigator.clipboard` API | `execCommand` fallback for HTTP |
| Redirects | Dynamic `<a>` injection | Bypasses popup blockers |

---

## Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome 90+ | ✅ Full |
| Safari 14+ (iOS & macOS) | ✅ Full |
| Firefox 88+ | ✅ Full |
| Edge 90+ | ✅ Full |
| Samsung Internet 14+ | ✅ Full |

> **Note:** The `navigator.clipboard` API requires HTTPS in production. A `document.execCommand('copy')` fallback is included for HTTP/local file environments.

---

## Deployment

### Option 1 — GitHub Pages (free)
1. Create a new GitHub repository
2. Upload `index.html` to the root
3. Go to **Settings → Pages → Source → main branch / root**
4. Your app is live at `https://yourusername.github.io/repo-name/`

### Option 2 — Netlify Drop (instant, free)
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag and drop the `index.html` file
3. Get a live URL instantly (e.g., `https://random-name.netlify.app`)
4. Optionally connect a custom domain

### Option 3 — Any static host
Upload `index.html` to any web server, AWS S3, Cloudflare Pages, Vercel, or cPanel hosting.

> ⚠️ **HTTPS is required** for clipboard copy to work in production. All the hosts above provide free HTTPS.

### Sharing with customers
- Display a **QR code print poster** at the counter pointing to the hosted URL
- Share the URL via WhatsApp broadcast or Instagram bio link

---

## Brand / CSS Variables Reference

| Variable | Default | Usage |
|----------|---------|-------|
| `--color-primary` | `#00458b` | Buttons, active pills, interactive elements |
| `--color-secondary` | `#eef5ff` | Card/pill idle backgrounds |
| `--color-accent` | `#e21b22` | Deselect hover state, CTA shadows |
| `--color-star` | `#ffc107` | Star fill, pulsing brand dot |
| `--color-whatsapp` | `#25D366` | WhatsApp button gradient |
| `--color-text-dark` | `#1a2340` | Headings, primary body text |
| `--color-text-mid` | `#445070` | Descriptions, pill labels |
| `--color-text-light` | `#7a8aaa` | Section labels, counters, placeholders |
| `--color-white` | `#ffffff` | Button text, card surfaces |
| `--color-bg` | `#f0f4fa` | Full-page background |
| `--color-card` | `#ffffff` | Section card background |
| `--color-border` | `#dce6f5` | Input borders, dividers |
| `--radius-sm` | `8px` | Small UI elements |
| `--radius-md` | `14px` | Cards, inputs, chips |
| `--radius-lg` | `22px` | Section cards |
| `--radius-xl` | `32px` | Large containers |
| `--font-body` | `'Inter', sans-serif` | All body and UI text |
| `--font-head` | `'Outfit', sans-serif` | Headings, badge text |
| `--transition` | `0.22s cubic-bezier(.4,0,.2,1)` | All animated transitions |

---

## License

This project is proprietary and built specifically for **Amul Ice Cream Parlour**. Do not redistribute without permission.

---

*Built with ❤️ as a zero-dependency single-file web app.*
