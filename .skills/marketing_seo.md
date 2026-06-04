# Antigravity Skill: Marketing, SEO & Growth
# Priority: MEDIUM | Impact: 8/10 | Rating: ⭐⭐⭐⭐

## ACTIVATION
Load when: building any public page, landing page, blog, or product page.
Traffic without SEO is luck. SEO without conversion is waste.

---

## CORE RULE
> The best product with zero distribution is invisible.
> Every page must be findable (SEO), fast (Core Web Vitals), and convertible (analytics).

---

## SEO IMPLEMENTATION CHECKLIST

### Meta Tags (Every Page)
```typescript
// Next.js App Router — generateMetadata function
export async function generateMetadata({ params }): Promise<Metadata> {
  return {
    title: 'Primary Keyword — Brand Name',  // 50-60 chars max
    description: 'Benefit + how + CTA. 150-160 characters max. Includes primary keyword.',
    keywords: ['keyword1', 'keyword2'],  // Less important but include
    openGraph: {
      title: 'Primary Keyword — Brand Name',
      description: 'OG description for social sharing',
      url: 'https://yourdomain.com/page',
      siteName: 'Brand Name',
      images: [{ url: 'https://yourdomain.com/og-image.png', width: 1200, height: 630 }],
      type: 'website',
    },
    twitter: {
      card: 'summary_large_image',
      title: 'Primary Keyword — Brand Name',
      description: 'Twitter card description',
      images: ['https://yourdomain.com/og-image.png'],
      creator: '@brandhandle',
    },
    canonical: 'https://yourdomain.com/page',
    robots: {
      index: true,
      follow: true,
      googleBot: { index: true, follow: true, 'max-image-preview': 'large' },
    },
  };
}
```

### JSON-LD Schema Markup (Per Page Type)
```typescript
// Product page
const productSchema = {
  '@context': 'https://schema.org',
  '@type': 'Product',
  name: 'Product Name',
  description: 'Product description',
  image: ['https://example.com/product.jpg'],
  brand: { '@type': 'Brand', name: 'Brand Name' },
  offers: {
    '@type': 'Offer',
    price: '29.00',
    priceCurrency: 'USD',
    availability: 'https://schema.org/InStock',
  },
  aggregateRating: {
    '@type': 'AggregateRating',
    ratingValue: '4.8',
    reviewCount: '2400',
  },
};

// Article/Blog page
const articleSchema = {
  '@context': 'https://schema.org',
  '@type': 'Article',
  headline: 'Article Title',
  datePublished: '2026-01-01T00:00:00Z',
  dateModified: '2026-06-01T00:00:00Z',
  author: { '@type': 'Person', name: 'Author Name' },
  publisher: { '@type': 'Organization', name: 'Brand Name' },
};

// FAQ page
const faqSchema = {
  '@context': 'https://schema.org',
  '@type': 'FAQPage',
  mainEntity: faqs.map(({ question, answer }) => ({
    '@type': 'Question',
    name: question,
    acceptedAnswer: { '@type': 'Answer', text: answer },
  })),
};
```

### Technical SEO Requirements
```
✅ Sitemap.xml auto-generated and submitted to Search Console
✅ robots.txt configured (allow all, except /api/* and /admin/*)
✅ Canonical URLs on all pages (prevent duplicate content)
✅ 301 redirects for all changed URLs (never 302 for permanent changes)
✅ Hreflang tags for multi-language sites
✅ Core Web Vitals passing (LCP < 2.5s, CLS < 0.1, INP < 200ms)
✅ Mobile-friendly (tested with Google's Mobile-Friendly Test)
✅ HTTPS on all pages
✅ No broken internal links
✅ Page depth < 3 clicks from homepage for important pages
✅ Breadcrumbs on all product/category/blog pages
```

### Internal Linking Strategy
```
Rule: Every page must link to at least 3 other relevant pages.
      Every important page must be reachable within 3 clicks from homepage.

Priority link targets:
  ├── High-converting pages (pricing, signup)
  ├── High-traffic pages (popular blog posts)
  └── Deep pages that otherwise get no internal links

Anchor text:
  ✅ Descriptive: "how to set up CI/CD pipelines"
  ❌ Generic: "click here" or "learn more"
```

---

## ANALYTICS ARCHITECTURE

### Event Tracking Schema
```typescript
// Define all events before building analytics
type AnalyticsEvent =
  | { name: 'page_view';     props: { page: string; referrer: string; utm_source?: string } }
  | { name: 'cta_click';     props: { button_id: string; section: string; variant: string } }
  | { name: 'signup_start';  props: { method: 'email' | 'google' | 'github' } }
  | { name: 'signup_complete'; props: { plan: string; source: string } }
  | { name: 'trial_start';   props: { plan: string; trial_days: number } }
  | { name: 'conversion';    props: { plan: string; revenue: number; currency: string } }
  | { name: 'feature_used';  props: { feature: string; first_time: boolean } }
  | { name: 'churn';         props: { plan: string; reason?: string; tenure_days: number } };

// Track every event through one function
function track(event: AnalyticsEvent) {
  posthog.capture(event.name, event.props);
  // Also send to data warehouse if needed
}
```

### Conversion Funnel Tracking
```
TOFU (Top of Funnel — Traffic):
  page_view → blog_read → newsletter_subscribe

MOFU (Middle — Consideration):
  feature_page_view → demo_watch → pricing_view

BOFU (Bottom — Conversion):
  signup_start → signup_complete → trial_start → conversion

Retention:
  feature_used → upgrade_prompt_seen → upgrade_completed
```

---

## PROGRAMMATIC SEO

```
For content-heavy products, generate pages programmatically:

Pattern: [Tool/Feature] + [Use Case] + [Location/Vertical]
Examples:
  /integrations/[tool-name]         → 200+ pages, one per integration
  /templates/[use-case]             → 100+ pages, one per template
  /compare/[competitor]             → 10-20 comparison pages
  /blog/[category]/[topic]          → Unlimited blog posts

Each programmatic page must have:
  ✅ Unique title and meta description (not templated)
  ✅ Unique body content (at least 60% unique)
  ✅ Internal links to related programmatic pages
  ✅ Schema markup appropriate for page type
  ✅ noindex if content quality is low (better no index than thin content)
```

---

## PERFORMANCE = SEO

```
Google ranks faster sites higher. Performance IS SEO.

Targets:
  LCP:  < 2.5s    (most critical for ranking)
  FID:  < 100ms
  CLS:  < 0.1     (prevent layout shift — common cause of poor score)
  TTFB: < 800ms   (server response time)

Quick wins:
  ✅ Preload LCP image with <link rel="preload">
  ✅ Self-host fonts (eliminate 3rd party request)
  ✅ Use CDN for all static assets
  ✅ Enable HTTP/3 / QUIC on server
  ✅ Compress all text responses with Brotli
  ✅ Lazy load all images below the fold
```

---

## WORLD-CLASS STACK
- astro: https://github.com/withastro/astro (⭐ 50k)
- next-seo: https://github.com/garmeeh/next-seo (⭐ 8k)
- posthog: https://github.com/PostHog/posthog (⭐ 25k)
- plausible: https://github.com/plausible/analytics (⭐ 22k)
- umami: https://github.com/umami-software/umami (⭐ 24k)
- schema-dts: https://github.com/google/schema-dts (⭐ 1.5k)
