# Antigravity Skill: Design System & Tokens
# Priority: CRITICAL | Impact: 9.5/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load when: creating ANY UI element, ANY component, ANY style.
Design tokens must exist BEFORE any component is built.

---

## CORE RULE
> ALL visual decisions are tokens. No component ever hard-codes a color, size, or spacing value.
> Dark/light mode is a single token swap — not duplicated styles.

---

## COLOR SYSTEM (HSL Token Architecture)

```css
:root {
  /* Brand Colors — HSL format ONLY */
  --color-brand-h: 220;          /* Hue */
  --color-brand-s: 80%;          /* Saturation */
  --color-brand-l: 55%;          /* Lightness */
  --color-brand: hsl(var(--color-brand-h) var(--color-brand-s) var(--color-brand-l));

  /* Semantic tokens (what the color means, not what it looks like) */
  --color-background:        hsl(0 0% 100%);
  --color-background-subtle: hsl(0 0% 97%);
  --color-surface:           hsl(0 0% 100%);
  --color-surface-raised:    hsl(0 0% 98%);
  --color-border:            hsl(0 0% 90%);
  --color-border-strong:     hsl(0 0% 80%);
  --color-text-primary:      hsl(0 0% 9%);
  --color-text-secondary:    hsl(0 0% 45%);
  --color-text-muted:        hsl(0 0% 65%);
  --color-text-on-brand:     hsl(0 0% 100%);

  /* Status colors */
  --color-success:   hsl(142 72% 40%);
  --color-warning:   hsl(38 92% 50%);
  --color-error:     hsl(0 84% 55%);
  --color-info:      hsl(199 89% 48%);
}

[data-theme="dark"] {
  /* Token values swap — component code stays unchanged */
  --color-background:        hsl(222 15% 8%);
  --color-background-subtle: hsl(222 15% 11%);
  --color-surface:           hsl(222 15% 12%);
  --color-surface-raised:    hsl(222 15% 16%);
  --color-border:            hsl(222 15% 22%);
  --color-border-strong:     hsl(222 15% 30%);
  --color-text-primary:      hsl(0 0% 95%);
  --color-text-secondary:    hsl(0 0% 65%);
  --color-text-muted:        hsl(0 0% 45%);
}
```

---

## TYPOGRAPHY SYSTEM

```css
:root {
  /* Font families — always pair display + body */
  --font-display: 'Instrument Serif', Georgia, serif;
  --font-body: 'DM Sans', system-ui, sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', monospace;

  /* Fluid type scale using clamp() */
  --text-xs:   clamp(0.70rem, 0.5vw + 0.55rem, 0.75rem);
  --text-sm:   clamp(0.85rem, 0.8vw + 0.60rem, 0.875rem);
  --text-base: clamp(1.00rem, 1.0vw + 0.70rem, 1.125rem);
  --text-lg:   clamp(1.10rem, 1.5vw + 0.75rem, 1.25rem);
  --text-xl:   clamp(1.20rem, 2.0vw + 0.80rem, 1.5rem);
  --text-2xl:  clamp(1.40rem, 2.5vw + 0.90rem, 1.875rem);
  --text-3xl:  clamp(1.70rem, 3.5vw + 1.00rem, 2.5rem);
  --text-4xl:  clamp(2.00rem, 5.0vw + 1.00rem, 3.5rem);
  --text-5xl:  clamp(2.50rem, 7.0vw + 1.00rem, 5.0rem);
  --text-6xl:  clamp(3.00rem, 9.0vw + 1.00rem, 7.0rem);

  /* Line heights */
  --leading-tight:  1.2;
  --leading-snug:   1.35;
  --leading-normal: 1.6;
  --leading-relaxed: 1.75;

  /* Font weights */
  --weight-regular:  400;
  --weight-medium:   500;
  --weight-semibold: 600;
  --weight-bold:     700;
  --weight-black:    900;
}
```

---

## SPACING SYSTEM (8-Point Grid)

```css
:root {
  --space-1:  4px;
  --space-2:  8px;
  --space-3:  12px;
  --space-4:  16px;
  --space-5:  20px;
  --space-6:  24px;
  --space-8:  32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;
  --space-20: 80px;
  --space-24: 96px;
  --space-32: 128px;
}
```

---

## SHADOW SYSTEM

```css
:root {
  --shadow-xs: 0 1px 2px 0 hsl(0 0% 0% / 0.05);
  --shadow-sm: 0 1px 3px 0 hsl(0 0% 0% / 0.08), 0 1px 2px -1px hsl(0 0% 0% / 0.08);
  --shadow-md: 0 4px 6px -1px hsl(0 0% 0% / 0.08), 0 2px 4px -2px hsl(0 0% 0% / 0.08);
  --shadow-lg: 0 10px 15px -3px hsl(0 0% 0% / 0.08), 0 4px 6px -4px hsl(0 0% 0% / 0.08);
  --shadow-xl: 0 20px 25px -5px hsl(0 0% 0% / 0.08), 0 8px 10px -6px hsl(0 0% 0% / 0.08);
  --shadow-brand: 0 0 0 3px hsl(var(--color-brand-h) var(--color-brand-s) 70% / 0.35);
}
```

---

## BORDER RADIUS SYSTEM

```css
:root {
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   12px;
  --radius-xl:   16px;
  --radius-2xl:  24px;
  --radius-full: 9999px;
}
```

---

## GLASSMORPHISM (Use With Purpose Only)

```css
.glass-card {
  background: hsl(var(--color-brand-h) var(--color-brand-s) 98% / 0.7);
  backdrop-filter: blur(16px) saturate(180%);
  -webkit-backdrop-filter: blur(16px) saturate(180%);
  border: 1px solid hsl(var(--color-brand-h) var(--color-brand-s) 80% / 0.2);
  border-radius: var(--radius-xl);
}

/* Dark mode variant */
[data-theme="dark"] .glass-card {
  background: hsl(222 15% 15% / 0.7);
  border-color: hsl(222 15% 30% / 0.4);
}
```

---

## ENFORCEMENT RULES

```
✅ ALL colors defined as CSS custom properties in :root
✅ ALL colors use HSL format — never raw hex or rgb in component files
✅ ALL font sizes use clamp() fluid scale variables
✅ ALL spacing uses 8-point grid variables
✅ Dark mode via [data-theme="dark"] attribute, never separate CSS files
✅ WCAG 2.2 AA contrast (4.5:1 for text, 3:1 for UI elements)
✅ Storybook story for every component showing all variants

❌ NEVER hardcode #hex colors in component files
❌ NEVER hardcode px sizes that don't use token variables
❌ NEVER use random glassmorphism without clear design purpose
❌ NEVER use more than 2 font families per project
❌ NEVER create new spacing values outside the 8-point system
```

---

## WORLD-CLASS REFERENCES
- radix-ui: https://github.com/radix-ui/primitives (⭐ 17k)
- shadcn-ui: https://github.com/shadcn-ui/ui (⭐ 82k)
- style-dictionary: https://github.com/amzn/style-dictionary (⭐ 4k)
- storybook: https://github.com/storybookjs/storybook (⭐ 85k)
- penpot: https://github.com/penpot/penpot (⭐ 35k)

## FONT SOURCES
- Google Fonts: https://fonts.google.com
- Fontshare: https://www.fontshare.com (Cabinet Grotesk, Satoshi, Clash Display)
- Fonts In Use: https://fontsinuse.com (inspiration)
