# Antigravity Skill: Mobile-First Responsive Design
# Priority: CRITICAL | Impact: 10/10 | Rating: ⭐⭐⭐⭐⭐
# 🆕 NEW SKILL

## ACTIVATION
Load when: building ANY public-facing website, landing page, or web app.
Mobile-first is NOT optional. 60%+ of web traffic is mobile.

---

## CORE RULE
> Design for the smallest screen first. Scale UP — never shrink down.
> If it works on mobile, it works everywhere. The reverse is rarely true.

---

## THE THUMB ZONE (Critical for Mobile UX)

```
Phone held in one hand — reachability map:
  ┌─────────────┐
  │  Hard to    │  ← Top of screen = difficult
  │   reach     │
  ├─────────────┤
  │  OK to      │  ← Middle = reachable with stretch
  │   reach     │
  ├─────────────┤
  │ Easy reach  │  ← Bottom = natural thumb zone
  │  (PRIME)    │
  └─────────────┘

Rule: Primary CTAs, nav, and key actions go in the bottom third.
      Never put the most important button at the top of a mobile screen.
```

---

## BREAKPOINT SYSTEM (Mobile-First Order)

```css
/* WRITE CSS IN THIS ORDER — always */

/* Base styles = Mobile (0px and up) — NO media query wrapper */
.hero {
  padding: var(--space-6) var(--space-4);
  font-size: var(--text-2xl);
}

/* Small: large phones (640px+) */
@media (min-width: 640px) {
  .hero { padding: var(--space-10) var(--space-8); }
}

/* Medium: tablets (768px+) */
@media (min-width: 768px) {
  .hero { font-size: var(--text-3xl); }
}

/* Large: desktop (1024px+) */
@media (min-width: 1024px) {
  .hero {
    padding: var(--space-20) var(--space-16);
    font-size: var(--text-5xl);
  }
}

/* XL: large desktop (1280px+) */
@media (min-width: 1280px) {}
```

---

## TOUCH TARGET RULES

```css
/* ALL interactive elements must meet minimum touch target */
button, a, input, select, textarea, [role="button"] {
  min-height: 48px;        /* Google's minimum: 48x48px */
  min-width: 48px;
  /* Add padding if visual size is smaller than 48px */
}

/* Spacing between adjacent touch targets */
.button-group > * + * {
  margin-left: var(--space-3);  /* Minimum 8px between targets */
}
```

---

## VIEWPORT & LAYOUT RULES

```css
/* CORRECT: Use dynamic viewport units (fixes iOS Safari bugs) */
.hero-section {
  min-height: 100dvh;    /* dvh = dynamic viewport height */
}

.modal {
  height: 100svh;         /* svh = small viewport height (excludes browser UI) */
}

/* WRONG: These cause bugs on iOS */
/* min-height: 100vh;  ← iOS Safari includes address bar, causes overflow */

/* Safe area for iOS notch and home bar */
.nav-bottom {
  padding-bottom: env(safe-area-inset-bottom, 16px);
}
.nav-top {
  padding-top: env(safe-area-inset-top, 0px);
}

/* Prevent horizontal overflow (common mobile bug) */
html, body {
  overflow-x: hidden;
  max-width: 100vw;
}
```

---

## TYPOGRAPHY ON MOBILE

```css
/* CRITICAL: Prevent iOS input auto-zoom */
/* Any input with font-size below 16px triggers iOS auto-zoom — breaks UX */
input, select, textarea {
  font-size: max(16px, 1rem);   /* Never below 16px */
}

/* Minimum readable body text on mobile */
body {
  font-size: var(--text-base);  /* min 16px equivalent */
  line-height: var(--leading-relaxed);
}

/* Headings scale fluidly — no fixed px on mobile */
h1 { font-size: var(--text-4xl); }  /* Uses clamp() from design_system.md */
h2 { font-size: var(--text-3xl); }
h3 { font-size: var(--text-2xl); }
```

---

## MOBILE NAVIGATION PATTERNS

```
Pattern 1: Bottom Navigation Bar (recommended for apps)
  ├── Fixed to bottom of viewport
  ├── 4-5 icons max with labels
  ├── Active state clearly visible
  └── Accounts for safe-area-inset-bottom

Pattern 2: Hamburger Menu (recommended for sites)
  ├── Slide-in from right (not top — top nav blocks content)
  ├── Close button in top-right of drawer
  ├── Overlay backdrop closes on tap
  └── Focus trap inside open drawer (accessibility)

Pattern 3: Sticky Top Bar (minimal, logo + 1 CTA)
  └── Collapses on scroll down, reveals on scroll up
```

---

## FORM BEST PRACTICES ON MOBILE

```html
<!-- Use correct inputmode for right keyboard on mobile -->
<input type="text"   inputmode="text"    />  <!-- Default keyboard -->
<input type="text"   inputmode="email"   />  <!-- Email keyboard with @ -->
<input type="text"   inputmode="tel"     />  <!-- Phone number keyboard -->
<input type="text"   inputmode="numeric" />  <!-- Number pad -->
<input type="text"   inputmode="decimal" />  <!-- Number pad with decimal -->
<input type="text"   inputmode="url"     />  <!-- URL keyboard with .com -->
<input type="text"   inputmode="search"  />  <!-- Search keyboard with Go -->

<!-- Autocomplete helps mobile users fill forms faster -->
<input autocomplete="email" />
<input autocomplete="name" />
<input autocomplete="tel" />
<input autocomplete="current-password" />
<input autocomplete="new-password" />
```

---

## IMAGE & MEDIA RULES

```html
<!-- CORRECT: Responsive image with srcset + WebP -->
<picture>
  <source
    srcset="hero-480.webp 480w, hero-768.webp 768w, hero-1280.webp 1280w"
    type="image/webp"
    sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
  />
  <img
    src="hero-768.jpg"
    alt="Descriptive alt text here"
    width="1280"
    height="720"
    loading="lazy"          /* All images below fold */
    decoding="async"
    style="aspect-ratio: 16/9; width: 100%; height: auto;"
  />
</picture>

<!-- Autoplay video: MUST have these attributes on mobile -->
<video autoplay muted loop playsinline poster="thumbnail.webp">
  <source src="hero.mp4" type="video/mp4" />
</video>
```

---

## MOBILE PERFORMANCE BUDGET

```
First Contentful Paint:   < 1.8s   on 4G connection
Largest Contentful Paint: < 2.5s   on 4G connection
Total Blocking Time:      < 200ms
Cumulative Layout Shift:  < 0.1

JavaScript (compressed):  < 150kb  first load
CSS (compressed):         < 30kb
Images:                   WebP format, lazy-loaded below fold
Fonts:                    Max 2 families, preloaded, font-display: swap
Total page weight:        < 1MB on first load (mobile 4G)

✅ Test with Chrome DevTools: Mobile throttling → Fast 3G
✅ Test with real devices (iOS Safari, Android Chrome)
✅ Run Lighthouse in CI on mobile preset
```

---

## GESTURES & TOUCH INTERACTIONS

```css
/* Improve scroll performance on mobile */
.scrollable-container {
  -webkit-overflow-scrolling: touch;
  scroll-behavior: smooth;
  overscroll-behavior: contain;   /* Prevent scroll chaining */
}

/* Prevent text selection on interactive elements */
.button, .card-interactive {
  -webkit-user-select: none;
  user-select: none;
  -webkit-tap-highlight-color: transparent;  /* Remove iOS tap flash */
}

/* Touch action for gesture-controlled elements */
.carousel {
  touch-action: pan-x;    /* Allow horizontal swipe only */
}
.map {
  touch-action: none;     /* Handle all touch events in JS */
}
```

---

## ANTI-PATTERNS TO BLOCK

```
❌ Fixed pixel widths: width: 1200px — use max-width instead
❌ vh units for hero: height: 100vh — use 100dvh
❌ Desktop hover-only interactions with no touch equivalent
❌ Input font-size below 16px — triggers iOS auto-zoom
❌ Small tap targets under 44px
❌ Horizontal scrollbar on any page
❌ Pop-ups that cover full screen on mobile on page load
❌ Videos without muted attribute (browsers block autoplay)
❌ Content that requires hover to reveal (hover doesn't exist on touch)
❌ Long tables that overflow on small screens (use responsive cards instead)
❌ Designing desktop → adding @media (max-width: 768px) overrides (wrong direction)
```

---

## TESTING CHECKLIST

```
Before shipping any public page, verify:
  □ iPhone SE (375px) — smallest common modern phone
  □ iPhone 14 Pro (393px) — most common iOS size
  □ Galaxy S22 (360px) — most common Android size
  □ iPad (768px) — tablet portrait
  □ Chrome DevTools responsive mode at 320px (smallest edge case)
  □ Touch targets all ≥ 48px
  □ No horizontal overflow
  □ iOS Safari: no content hidden behind notch/home bar
  □ Lighthouse mobile score > 90
  □ CLS < 0.1 (no layout shift on font/image load)
```
