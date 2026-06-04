# Antigravity Skill: Animation & Motion Design
# Priority: HIGH | Impact: 9/10 | Rating: ⭐⭐⭐⭐½
# 🆕 NEW SKILL

## ACTIVATION
Load when: building any public website, landing page, or interactive UI.
Motion makes interfaces feel alive, premium, and trustworthy.

---

## CORE RULE
> Every animation must serve a purpose. Motion is communication, not decoration.
> If you can't explain WHY an element animates, remove the animation.

---

## THE 4 LAWS OF MOTION

```
Law 1 — PURPOSE
  Every animation communicates something:
  - Guide attention (show what to look at next)
  - Indicate state change (loading, success, error)
  - Confirm action (button press feedback)
  - Delight (micro-rewards for interaction)

Law 2 — PHYSICS
  All movement follows natural physics:
  - Things accelerate as they start, decelerate as they stop
  - Use spring physics (not linear) for organic feel
  - Ease-out for elements entering the screen
  - Ease-in for elements leaving the screen
  - Spring for interactive/physics-based animations

Law 3 — PERFORMANCE
  Animations must never hurt Core Web Vitals:
  - ONLY animate: transform, opacity, filter
  - NEVER animate: width, height, top, left, margin, padding
  - These cause layout reflow = jank = poor Lighthouse score

Law 4 — RESPECT
  Always honor user preferences:
  - @media (prefers-reduced-motion: reduce) — provide static fallback
  - Never autoplay audio with animation
  - No flashing > 3Hz (WCAG seizure prevention)
```

---

## PERFORMANCE RULE (Non-Negotiable)

```css
/* SAFE TO ANIMATE (GPU-composited, no reflow) */
transform: translateX(), translateY(), scale(), rotate()
opacity: 0 → 1
filter: blur(), brightness()

/* NEVER ANIMATE (causes layout reflow = jank) */
/* width, height, top, right, bottom, left        */
/* margin, padding, border-width                  */
/* font-size, line-height                         */

/* GPU promotion for animated elements */
.animated-element {
  will-change: transform;     /* Use sparingly — only on elements that actually animate */
  transform: translateZ(0);   /* Forces GPU layer */
}

/* Always remove will-change after animation completes */
element.addEventListener('transitionend', () => {
  element.style.willChange = 'auto';
});
```

---

## PREFERS-REDUCED-MOTION (Required)

```css
/* Default: full animations */
.fade-in {
  animation: fadeIn 0.6s ease-out forwards;
}

/* Reduced motion: instant state change, no animation */
@media (prefers-reduced-motion: reduce) {
  .fade-in {
    animation: none;
    opacity: 1;
  }
  
  /* Global: remove all transitions and animations */
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## ENTRANCE ANIMATIONS (Page Load & Route Change)

```css
/* Base keyframes */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.95); }
  to   { opacity: 1; transform: scale(1); }
}

/* Stagger cascade for hero sections */
.hero-headline  { animation: fadeUp 0.6s ease-out 0ms   both; }
.hero-subtext   { animation: fadeUp 0.6s ease-out 100ms both; }
.hero-cta       { animation: fadeUp 0.6s ease-out 200ms both; }
.hero-image     { animation: scaleIn 0.8s ease-out 300ms both; }
.hero-social    { animation: fadeIn 0.5s ease-out 500ms both; }
```

```typescript
// Framer Motion (React) — preferred for component animations
import { motion } from 'framer-motion';

const fadeUpVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: (i: number) => ({
    opacity: 1,
    y: 0,
    transition: { delay: i * 0.1, duration: 0.6, ease: [0.22, 1, 0.36, 1] }
  }),
};

// Usage with stagger
{items.map((item, i) => (
  <motion.div
    key={item.id}
    custom={i}
    initial="hidden"
    animate="visible"
    variants={fadeUpVariants}
  />
))}
```

---

## SCROLL-DRIVEN ANIMATIONS

```typescript
// IntersectionObserver — reveal on scroll (lightweight, no library)
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible');
        observer.unobserve(entry.target); // Only trigger once
      }
    });
  },
  { threshold: 0.1, rootMargin: '0px 0px -50px 0px' }
);

document.querySelectorAll('[data-animate]').forEach((el) => observer.observe(el));
```

```css
/* Paired CSS */
[data-animate] {
  opacity: 0;
  transform: translateY(24px);
  transition: opacity 0.6s ease-out, transform 0.6s ease-out;
}

[data-animate].visible {
  opacity: 1;
  transform: translateY(0);
}

/* Stagger delays for lists */
[data-animate]:nth-child(1) { transition-delay: 0ms; }
[data-animate]:nth-child(2) { transition-delay: 80ms; }
[data-animate]:nth-child(3) { transition-delay: 160ms; }
[data-animate]:nth-child(4) { transition-delay: 240ms; }
```

---

## SCROLL SMOOTHNESS (Lenis)

```typescript
// Replace native scroll with smooth physics scroll
import Lenis from 'lenis';

const lenis = new Lenis({
  duration: 1.2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  smoothWheel: true,
});

function raf(time: number) {
  lenis.raf(time);
  requestAnimationFrame(raf);
}
requestAnimationFrame(raf);

// Connect to GSAP ScrollTrigger
lenis.on('scroll', ScrollTrigger.update);
```

---

## MICRO-INTERACTIONS

```css
/* Button press feedback (spring physics) */
.button {
  transition: transform 0.1s ease-out, box-shadow 0.2s ease-out;
}
.button:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}
.button:active {
  transform: translateY(0) scale(0.97);
  box-shadow: var(--shadow-sm);
}

/* Card lift on hover */
.card {
  transition: transform 0.25s cubic-bezier(0.22, 1, 0.36, 1),
              box-shadow 0.25s ease-out;
}
.card:hover {
  transform: translateY(-6px);
  box-shadow: var(--shadow-xl);
}

/* Link underline animation */
.nav-link {
  position: relative;
}
.nav-link::after {
  content: '';
  position: absolute;
  bottom: -2px; left: 0;
  width: 0; height: 2px;
  background: var(--color-brand);
  transition: width 0.25s ease-out;
}
.nav-link:hover::after { width: 100%; }

/* Input focus ring */
input {
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}
input:focus {
  border-color: var(--color-brand);
  box-shadow: 0 0 0 3px hsl(var(--color-brand-h) var(--color-brand-s) 70% / 0.25);
  outline: none;
}
```

---

## PAGE TRANSITIONS (React / Next.js)

```typescript
// Framer Motion AnimatePresence for route changes
import { AnimatePresence, motion } from 'framer-motion';

const pageVariants = {
  initial: { opacity: 0, y: 8 },
  enter:   { opacity: 1, y: 0, transition: { duration: 0.35, ease: [0.22, 1, 0.36, 1] } },
  exit:    { opacity: 0, y: -8, transition: { duration: 0.2, ease: 'easeIn' } },
};

// Wrap page content
<AnimatePresence mode="wait">
  <motion.div
    key={router.pathname}
    variants={pageVariants}
    initial="initial"
    animate="enter"
    exit="exit"
  >
    {children}
  </motion.div>
</AnimatePresence>
```

---

## HERO SECTION MOTION PATTERNS

```
Pattern 1: Gradient Shift
  background: animated hue-rotate on gradient — slow, 10s loop, subtle

Pattern 2: Floating 3D Object
  Embed Spline 3D scene — adds premium feel with zero custom 3D coding

Pattern 3: Typewriter / Rotating Headlines
  Cycle through value propositions with crossfade (not typing — it's overused)

Pattern 4: Particle System
  Use tsParticles or custom canvas for subtle particle network background

Pattern 5: Video Background
  Muted, autoplay, loop, playsinline — overlay with semi-transparent gradient
  Always provide poster frame for slow connections
```

---

## LOADING STATES

```css
/* Skeleton screen (better than spinners) */
.skeleton {
  background: linear-gradient(
    90deg,
    var(--color-border) 25%,
    var(--color-background-subtle) 50%,
    var(--color-border) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  border-radius: var(--radius-md);
}

@keyframes shimmer {
  from { background-position: 200% 0; }
  to   { background-position: -200% 0; }
}

/* Spinner (for actions, not page load) */
.spinner {
  width: 20px; height: 20px;
  border: 2px solid var(--color-border);
  border-top-color: var(--color-brand);
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

---

## WORLD-CLASS ANIMATION STACK
- framer-motion: https://github.com/framer/motion (⭐ 25k)
- gsap: https://github.com/greensock/GSAP (⭐ 21k)
- lenis: https://github.com/darkroomengineering/lenis (⭐ 8k)
- three.js: https://github.com/mrdoob/three.js (⭐ 102k)
- react-three-fiber: https://github.com/pmndrs/react-three-fiber (⭐ 27k)
- auto-animate: https://github.com/formkit/auto-animate (⭐ 12k)
- remotion: https://github.com/remotion-dev/remotion (⭐ 22k)
- Spline 3D: https://spline.design
