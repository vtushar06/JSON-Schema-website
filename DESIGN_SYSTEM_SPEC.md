# JSON Schema Website - Design System Specification

## 1. COLOR SYSTEM

### Primary Brand Colors
```
Primary Blue:      #002CC4 (RGB: 0, 44, 196)
Secondary Blue:    #5468FF (RGB: 84, 104, 255)
Accent Orange:     #F47A08 (RGB: 244, 122, 8)
Dark Blue:         #001a8b (RGB: 0, 26, 139) [for hover states]
Light Blue:        #3d5eff (RGB: 61, 94, 255) [for light backgrounds]
```

### Neutral Gray Scale
```
Neutral 50:   #F9FAFB  (Lightest - Backgrounds)
Neutral 100:  #F3F4F6  (Very light)
Neutral 200:  #E5E7EB  (Light borders)
Neutral 300:  #D1D5DB  (Subtle borders)
Neutral 400:  #9CA3AF  (Placeholder text)
Neutral 500:  #6B7280  (Secondary text)
Neutral 600:  #4B5563  (Strong text)
Neutral 700:  #374151  (Primary text)
Neutral 800:  #1F2937  (Dark text)
Neutral 900:  #111827  (Darkest)
```

### Semantic Colors
```
Success:  #10B981 (Green)
Warning:  #F59E0B (Amber)
Error:    #EF4444 (Red)
Info:     #3B82F6 (Blue)
```

### Dark Mode Overrides
```
Background:       #1F2937 (Neutral 800)
Surface Primary:  #111827 (Neutral 900)
Surface Secondary:#2D3748 (Neutral 700)
Text Primary:     #F9FAFB (Neutral 50)
Text Secondary:   #D1D5DB (Neutral 300)
Border:           #374151 (Neutral 700)
```

### Color Contrast Ratios (WCAG 2.1)
```
✓ Primary Blue on White:        13.5:1 (AAA Pass)
✓ Neutral 600 on White:          7.1:1 (AAA Pass)
✓ Neutral 700 on White:          9.5:1 (AAA Pass)
✗ Neutral 400 on White:          4.5:1 (AA Pass, need AAA)
✓ White on Primary Blue:        13.5:1 (AAA Pass)
✓ Error on White:                5.2:1 (AA Pass)
```

---

## 2. TYPOGRAPHY SYSTEM

### Font Family
```
Primary:   Inter
           Font weights: 300, 400, 500, 600, 700, 800, 900
           Used for: Body, headings, buttons, labels

Monospace: JetBrains Mono
           Used for: Code blocks, pre-formatted text

Fallbacks: system-ui, -apple-system, sans-serif
```

### Type Scale & Hierarchy

#### Headings
```
H1  Desktop: 60px / 1.2 line-height / 700 weight
    Mobile:  35px / 1.2 line-height / 700 weight
    Letter-spacing: -0.02em

H2  Desktop: 45px / 1.2 line-height / 700 weight
    Mobile:  28px / 1.2 line-height / 700 weight
    Letter-spacing: -0.01em

H3  Desktop: 35px / 1.3 line-height / 600 weight
    Mobile:  25px / 1.3 line-height / 600 weight

H4  Desktop: 25px / 1.4 line-height / 600 weight
    Mobile:  22px / 1.4 line-height / 600 weight

H5  Desktop: 20px / 1.5 line-height / 600 weight
    Mobile:  20px / 1.5 line-height / 600 weight

H6  Desktop: 16px / 1.5 line-height / 600 weight
    Mobile:  16px / 1.5 line-height / 600 weight
```

#### Body Text
```
Large:  18px / 1.6 line-height / 400 weight
        Used for: Section introductions, feature descriptions

Regular: 16px / 1.6 line-height / 400 weight (DEFAULT)
        Used for: Body paragraphs, descriptions

Small:   14px / 1.5 line-height / 400 weight
        Used for: Captions, helper text, metadata

XSmall:  12px / 1.4 line-height / 500 weight
        Used for: Badges, tags, timestamps
```

#### Special Styles
```
Label/Button: 14px / 1.5 line-height / 600 weight
Link:        16px / 1.6 line-height / 500 weight + underline on hover
Code:        14px / 1.5 line-height / 400 weight (JetBrains Mono)
```

### Optimal Line Length
```
Desktop: 65-75 characters (850px-950px)
Mobile:  35-45 characters (full width with padding)
```

---

## 3. SPACING SYSTEM

### Base Unit: 4px (Allows: 4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48)

### Standard Spacing Values
```
xs:  4px      (text-to-icon padding)
sm:  8px      (tight spacing)
md:  12px     (small component padding)
lg:  16px     (standard padding)
xl:  24px     (large padding, section spacing)
2xl: 32px     (section gaps)
3xl: 48px     (hero section spacing)
4xl: 64px     (major section spacing)
```

### Component Padding
```
Button:        12px (vertical) × 16px (horizontal) [md]
Card:          24px [xl]
Section:       32px [2xl]
Container:     16px (mobile) / 24px (desktop) [xl]
Input:         12px [md]
```

### Margin & Gaps
```
Element margin bottom (text):  16px [lg]
Heading margin bottom:         24px [xl]
Section margin:                64px [4xl] desktop / 32px [2xl] mobile
Component gap:                 16px [lg]
Large gap:                     24px [xl]
```

---

## 4. COMPONENT SYSTEM

### Buttons

#### Variants
```
Primary Button
├─ Default:    bg-primary text-white
├─ Hover:      bg-blue-700 shadow-lg
├─ Active:     scale-95
├─ Disabled:   opacity-50 cursor-not-allowed
└─ Focus:      ring-2 ring-primary ring-offset-2

Secondary Button
├─ Default:    bg-gray-200 text-gray-900
├─ Hover:      bg-gray-300
├─ Dark mode:  bg-gray-700 text-white
└─ Focus:      ring-2 ring-gray-500 ring-offset-2

Outline Button
├─ Default:    border-2 border-primary text-primary
├─ Hover:      bg-primary/5
├─ Dark mode:  border-blue-400 text-blue-400
└─ Focus:      ring-2 ring-primary ring-offset-2

Ghost Button
├─ Default:    transparent text-gray-700
├─ Hover:      bg-gray-100
├─ Dark mode:  text-gray-300 hover:bg-gray-700
└─ Focus:      ring-2 ring-primary ring-offset-2
```

#### Sizes
```
Small (sm):    px-3 py-1.5 text-sm   [icon + text or text only]
Medium (md):   px-4 py-2 text-base   [default for most uses]
Large (lg):    px-6 py-3 text-lg     [hero CTAs]
XLarge (xl):   px-8 py-4 text-xl     [prominent CTAs]
```

#### State Transitions
```
Default → Hover:   duration-200 ease-in-out
Hover → Active:    scale-95 immediate
Default → Focus:   ring-2 with offset
Disabled:          opacity-50, no interactions
Loading:           spinner icon + text "Loading..."
```

### Cards

#### Variants
```
Elevated (default)
├─ Background:     white / dark:slate-800
├─ Border:         1px gray-200 / dark:gray-700
├─ Shadow:         shadow-lg dark:shadow-lg dark:shadow-slate-900/50
├─ Hover (if interactive): scale-102 shadow-xl
└─ Transition:      duration-300

Outlined
├─ Background:     white / dark:slate-800
├─ Border:         2px gray-200 / dark:gray-700
├─ Shadow:         shadow-sm
└─ Hover:          scale-102

Flat
├─ Background:     gray-50 / dark:gray-800
├─ Border:         1px gray-200 / dark:gray-700
├─ Shadow:         none
└─ Hover:          bg-gray-100 / dark:bg-gray-700
```

#### Card Anatomy
```
┌─────────────────────────────┐
│ [Icon] [Title]              │
├─────────────────────────────┤
│                             │
│ Card body text can span     │
│ multiple lines with proper  │
│ line-height and spacing.    │
│                             │
│                  Read More → │
└─────────────────────────────┘

Icon Size:      48px × 48px
Title:          font-semibold text-lg
Body:           text-base line-height-1.6
Separator:      1px gray-300 / dark:gray-600
Padding:        24px [xl]
Gap (icon-title): 16px [lg]
```

### Forms & Inputs

#### Input Field
```
Default
├─ Border:      1px gray-300
├─ Padding:     12px [md]
├─ Height:      40px
├─ Background:  white / dark:slate-800
└─ Text:        gray-900 / dark:white

Hover
├─ Border:      1px gray-400
└─ Shadow:      0 0 0 3px rgba(primary, 0.1)

Focus
├─ Border:      2px primary
├─ Shadow:      0 0 0 4px rgba(primary, 0.1)
└─ Outline:     none

Disabled
├─ Background:  gray-100 / dark:gray-700
├─ Color:       gray-500
└─ Cursor:      not-allowed

Error
├─ Border:      2px error-red
├─ Icon:        error-red
└─ Message:     text-sm text-error-red
```

#### Select/Dropdown
```
Same as input field + dropdown arrow icon (right-aligned)
Open state: border-primary, visible options
Option hover: bg-gray-100 / dark:bg-gray-700
Selected option: bg-primary/10 text-primary
```

### Navigation

#### Main Navigation Bar
```
Height:         64px (with padding)
Background:     white / dark:slate-800
Shadow:         shadow-xl drop-shadow-lg
Position:       fixed top-0 z-[170]
Padding:        16px (h) × 32px (h)

Nav Links
├─ Active:      text-primary dark:text-white dark:underline
├─ Inactive:    text-gray-700 dark:text-gray-200
├─ Hover:       text-primary dark:hover:underline
├─ Focus:       ring-2 ring-primary
└─ Transition:  duration-200

Mobile Menu Button
├─ Size:        32px × 32px
├─ Padding:     8px
├─ Hover:       bg-gray-100 / dark:bg-gray-700
└─ Hidden:      lg and above
```

#### Mobile Navigation
```
Position:       fixed top-16 left-0 z-[190]
Width:          100%
Background:     white / dark:slate-800
Max-height:     calc(100vh - 4rem)
Overflow:       y-auto

Animation
├─ Open:        duration-300
├─ Close:       duration-300
└─ Easing:      ease-in-out
```

#### Breadcrumbs
```
Text size:      14px
Separator:      "/" or chevron icon
Gap:            8px
Link color:     primary
Link hover:     underline + text-primary-dark
Current:        text-gray-600 (not clickable)
```

### Badges & Tags

#### Badge
```
Padding:        4px (v) × 8px (h)
Font size:      12px
Font weight:    600
Border-radius:  4px
Background:     primary-light / gray-200
Text:           white / gray-900
```

#### Tag
```
Padding:        6px (v) × 12px (h)
Font size:      14px
Border-radius:  16px (rounded-full)
Border:         1px gray-300
Background:     gray-50 / dark:gray-800
Hover:          bg-gray-100 / dark:bg-gray-700
Close button:   8px margin-left
```

---

## 5. SHADOW SYSTEM

### Depth Levels
```
Shallow (sm):     0px 2px 4px rgba(0, 0, 0, 0.05)
Light (md):       0px 4px 8px rgba(0, 0, 0, 0.1)
Medium (lg):      0px 10px 20px rgba(0, 0, 0, 0.12)
Deep (xl):        0px 20px 40px rgba(0, 0, 0, 0.15)

Dark Mode Overrides
Shallow-dark:     0px 2px 4px rgba(0, 0, 0, 0.5)
Light-dark:       0px 4px 8px rgba(0, 0, 0, 0.5)
Medium-dark:      0px 10px 20px rgba(0, 0, 0, 0.6)
Deep-dark:        0px 20px 40px rgba(0, 0, 0, 0.7)
```

### Usage
```
Cards:           shadow-lg
Modals/Overlays: shadow-xl
Buttons (hover): shadow-lg
Inputs (focus):  shadow-md inset
```

---

## 6. BORDER RADIUS SYSTEM

```
xs:    2px    (input borders, small components)
sm:    4px    (buttons, badges)
md:    8px    (cards, modals)
lg:    12px   (larger sections)
full:  9999px (pills, rounded buttons)
```

---

## 7. ANIMATION & TRANSITION SYSTEM

### Transition Durations
```
Fast:       100ms (immediate feedback)
Standard:   200ms (default, most animations)
Slow:       300ms (complex animations)
Slowest:    500ms (entrance animations)
```

### Easing Functions
```
ease-in-out (default):  smooth, natural feel
ease-in:                for exit animations
ease-out:               for entrance animations
ease-linear:            for continuous motion
```

### Standard Animations
```
Fade In:       opacity 0→1, duration-300
Slide In:      translateX/Y, duration-300
Scale:         scale 0.95→1, duration-300
Pulse:         opacity pulse, infinite, duration-2000
Spin:          rotate 360deg, infinite, duration-1000
```

### Micro-interactions
```
Button press:   scale-95 active:scale-95
Hover lift:     -translate-y-1 hover:shadow-lg
Menu open:      slide-in-from-left duration-300
Tooltip:        fade-in duration-200
Loading:        spin infinite duration-1000
```

---

## 8. RESPONSIVE BREAKPOINTS

```
Mobile:        320px - 639px   (default)
Tablet:        640px - 1023px  (sm, md)
Desktop:       1024px - 1279px (lg)
Large Desktop: 1280px - 1535px (xl)
XL Desktop:    1536px+         (2xl)
```

### Mobile-First Approach
```
Default:       mobile styles (no prefix)
sm:            640px and up
md:            768px and up
lg:            1024px and up
xl:            1280px and up
2xl:           1536px and up
```

---

## 9. ELEVATION & Z-INDEX SYSTEM

```
Background layer:     z-0
Default layer:        z-10
Sticky elements:      z-20 (sticky headers)
Dropdowns/Popovers:   z-40 (position relative)
Mobile menu overlay:  z-50 (fixed overlay)
Modals/Dialogs:       z-50 (highest content)
Tooltips:             z-50 (above modals when needed)
Notifications:        z-50 (top-right position)
```

### Header & Navigation
```
Header:        z-[170] (fixed)
Mobile nav:    z-[190] (fixed overlay)
Dropdown:      z-10 (within header context)
```

---

## 10. ACCESSIBILITY SPECIFICATIONS

### Focus Management
```
All interactive elements MUST have visible focus indicator:
- 2px ring in primary color
- 2px offset from element
- Visible on light AND dark backgrounds

Exception: Skip links (hidden by default, visible on focus)
```

### Color Contrast (WCAG 2.1)
```
Normal text:      Minimum AA (4.5:1), AAA preferred (7:1)
Large text:       Minimum AA (3:1), AAA preferred (4.5:1)
UI Components:    Minimum 3:1
```

### ARIA Labels
```
Navigation items:  aria-label for icon-only buttons
Form inputs:       aria-label or associated label
Buttons:           aria-pressed/expanded for toggle states
Dynamic content:   aria-live regions for updates
Images:            alt text (descriptive, not redundant)
Decorative icons:  aria-hidden="true"
```

### Keyboard Navigation
```
Tab order:         Natural, logical flow (top-to-bottom, left-to-right)
Focus trap:        Modals should trap focus
Escape key:        Close dropdowns, modals, overlays
Enter/Space:       Activate buttons, checkboxes, radio buttons
Arrow keys:        Navigate within select lists, sliders
```

---

## 11. DARK MODE SPECIFICATIONS

### Colors in Dark Mode
```
Text Primary:      Neutral 50 (#F9FAFB)
Text Secondary:    Neutral 300 (#D1D5DB)
Background:        Neutral 800 (#1F2937)
Surface Primary:   Neutral 900 (#111827)
Surface Secondary: Neutral 700 (#374151)
Border:            Neutral 700 (#374151)
```

### Component Adjustments
```
Buttons:           Softer shadows, maintained contrast
Cards:             Darker backgrounds, subtle borders
Inputs:            Dark backgrounds, light borders
Overlays:          Semitransparent, not fully opaque
Gradients:         Darker color stops
```

### No Color Inversion
```
❌ DO NOT invert images with filter: invert(1)
✓ DO provide separate dark mode assets
✓ DO use dark-specific color variants
```

---

## 12. IMAGE & MEDIA SPECIFICATIONS

### Image Sizes
```
Hero image:       1920×1080px (desktop), 768×400px (mobile)
Card image:       600×400px (16:9 aspect ratio)
Icon:             24-48px (SVG preferred)
Logo:             200-300px width
Avatar:           40-48px (circular)
Background image: 1920×1200px minimum
```

### Image Formats
```
Photographs:      WebP with JPG fallback
Icons:            SVG (preferred), PNG (48px max)
Logos:            SVG (preferred)
Illustrations:    SVG or WebP
Screenshots:      WebP or PNG
```

### Image Optimization
```
Max file size:     200KB (average)
Lazy loading:      Below-the-fold images
Next.js Image:     Use for all dynamic images
Responsive sizes:  Define for different breakpoints
```

---

## 13. LAYOUT SPECIFICATIONS

### Container Widths
```
Mobile:           100% with 16px padding
Tablet:           90% max-width 750px
Desktop:          85% max-width 1200px
Large desktop:    max-width 1400px
```

### Section Spacing
```
Mobile:           32px top/bottom padding
Desktop:          64px top/bottom padding
Horizontal gap:   24px between columns
Vertical gap:     32px between sections
```

### Grid Systems
```
Mobile:           1 column
Tablet:           2 columns (md: grid-cols-2)
Desktop:          3-4 columns (lg: grid-cols-3 or lg:grid-cols-4)
XL Desktop:       4-6 columns (xl: grid-cols-4 or 2xl:grid-cols-6)
```

---

## 14. TYPOGRAPHY USAGE GUIDE

### Headings (H1-H6)
```
Use one H1 per page (page title)
Use H2 for major sections
Use H3 for subsections
Don't skip heading levels
Use semantic HTML (not just for styling)
```

### Body Text
```
Standard: 16px / 1.6 line-height
Paragraphs: separated by 16px margin
Lists: 16px with 8px item gap
Emphasis: use <strong> (not bold class)
Links: underline on hover (in body text)
```

### Code & Technical Text
```
Font: JetBrains Mono, 14px
Line-height: 1.5
Letter-spacing: normal
Padding: 4px horizontal (inline code)
Block code: 24px padding, 16px margin
```

---

## 15. STATE INDICATORS

### Loading State
```
Skeleton screens: 100% width, animate-pulse
Loading spinner: centered, size-6
Loading text:    "Loading..." with spinner
Duration:        indefinite until complete
```

### Empty State
```
Icon:             128×128px, gray-400
Heading:          "No items found"
Description:      Helpful message
CTA:              Optional action button
```

### Error State
```
Icon:             error-red, 64×64px
Heading:          "Something went wrong"
Message:          Error description
CTA:              Retry button
```

### Success State
```
Icon:             success-green, 64×64px
Message:          "Successfully completed"
Duration:         auto-dismiss after 3s
Animation:        fade-in then fade-out
```

---

## Design System Version Control

```
Current Version:  1.0.0
Last Updated:     November 2025
Next Review:      Q1 2026
Maintained By:    Design & Engineering team
```

---

## Quick Reference Links

- **Tailwind Config:** `tailwind.config.js`
- **Global Styles:** `styles/globals.css`
- **Components:** `components/` directory
- **Icons:** Lucide React (lucide.dev)
- **UI Kit:** shadcn/ui (ui.shadcn.com)

---

## Common Customizations

### Custom Colors
```tsx
// Use Tailwind's extend feature
className='text-primary hover:text-blue-700'
className='dark:text-gray-200'
```

### Custom Spacing
```tsx
// Always use Tailwind scale (multiples of 4px)
className='p-4' /* 16px */
className='gap-6' /* 24px */
className='mb-8' /* 32px */
```

### Custom Shadows
```tsx
// Choose appropriate depth
className='shadow-sm'   /* light */
className='shadow-md'   /* medium */
className='shadow-lg'   /* deep */
className='shadow-xl'   /* very deep */
```

---

## Future Enhancements

- [ ] Dynamic theme switching in Storybook
- [ ] Design token export to JSON/CSS
- [ ] Component documentation site
- [ ] Design handoff with developers
- [ ] Accessibility testing automation
- [ ] Performance monitoring integration
- [ ] Version tracking for design changes
