# JSON Schema Website - UI/UX Improvements Implementation Guide

## Quick Reference: Top 20 Improvements to Make

### 1. NAVIGATION ACCESSIBILITY
**File:** `components/Layout.tsx` (lines 99-296)

**Issue:** Missing aria-labels and keyboard accessibility

**Current Code:**
```jsx
<div onClick={() => useStore.setState({ overlayNavigation: 'docs' })}>
  <div className='block lg:hidden space-y-2 items-center'>
    <div className={`w-6 h-1 ${menu} rounded`}></div>
    <div className={`w-6 h-1 ${menu} rounded`}></div>
    <div className={`w-6 h-1 ${menu} rounded`}></div>
  </div>
</div>
```

**Improved Code:**
```jsx
<button
  aria-label="Open navigation menu"
  aria-expanded={showMobileNav}
  aria-controls="mobile-nav"
  onClick={() => useStore.setState({ overlayNavigation: 'docs' })}
  className='lg:hidden p-2 hover:bg-gray-100 dark:hover:bg-gray-700 rounded-md transition'
>
  <div className='block space-y-2 items-center'>
    <div className={`w-6 h-1 ${menu} rounded transition-transform`}></div>
    <div className={`w-6 h-1 ${menu} rounded transition-transform`}></div>
    <div className={`w-6 h-1 ${menu} rounded transition-transform`}></div>
  </div>
</button>
```

---

### 2. LINK CONTRAST IMPROVEMENT
**File:** `components/Layout.tsx` (lines 164-169)

**Issue:** text-slate-600 has insufficient contrast (4.5:1, needs AAA 7:1)

**Current Code:**
```jsx
{
  'text-primary dark:text-white dark:underline hover:text-primary':
    isActiveNav,
  'text-slate-600 dark:text-white hover:text-primary dark:hover:underline':
    !isActiveNav,
}
```

**Improved Code:**
```jsx
{
  'text-primary dark:text-white dark:underline hover:text-primary transition-colors':
    isActiveNav,
  'text-gray-700 dark:text-gray-200 hover:text-primary dark:hover:underline transition-colors':
    !isActiveNav,
}
```

---

### 3. BUTTON CONSISTENCY SYSTEM
**File:** Create new file `components/ui/Button.tsx`

**Issue:** Buttons scattered throughout with different styles

**Implementation:**
```tsx
import React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded font-medium ' +
  'transition-all duration-200 ' +
  'focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-primary ' +
  'disabled:opacity-50 disabled:cursor-not-allowed ' +
  'active:scale-95',
  {
    variants: {
      variant: {
        primary:
          'bg-primary text-white hover:bg-blue-700 hover:shadow-lg ' +
          'dark:shadow-lg dark:hover:shadow-blue-500/50',
        secondary:
          'bg-gray-200 text-gray-900 hover:bg-gray-300 ' +
          'dark:bg-gray-700 dark:text-white dark:hover:bg-gray-600',
        outline:
          'border-2 border-primary text-primary ' +
          'hover:bg-primary hover:text-white ' +
          'dark:border-blue-400 dark:text-blue-400',
        ghost:
          'text-gray-700 hover:bg-gray-100 ' +
          'dark:text-gray-300 dark:hover:bg-gray-700',
      },
      size: {
        sm: 'px-3 py-1.5 text-sm',
        md: 'px-4 py-2 text-base',
        lg: 'px-6 py-3 text-lg',
        xl: 'px-8 py-4 text-xl',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean;
  loading?: boolean;
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, loading, disabled, children, ...props }, ref) => (
    <button
      ref={ref}
      className={cn(buttonVariants({ variant, size, className }))}
      disabled={loading || disabled}
      {...props}
    >
      {loading ? (
        <>
          <svg className='animate-spin -ml-1 mr-3 h-4 w-4' xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24'>
            <circle className='opacity-25' cx='12' cy='12' r='10' stroke='currentColor' strokeWidth='4'></circle>
            <path className='opacity-75' fill='currentColor' d='M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z'></path>
          </svg>
          Loading...
        </>
      ) : (
        children
      )}
    </button>
  )
);

Button.displayName = 'Button';

export { Button, buttonVariants };
```

---

### 4. FOCUS STYLES SYSTEM
**File:** `styles/globals.css`

**Add:**
```css
@layer components {
  /* Focus styles for all interactive elements */
  @apply focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 dark:focus:ring-offset-slate-800;

  /* Specific focus management */
  .focus-ring {
    @apply focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2;
  }

  /* Remove default focus on Mac */
  input:focus,
  button:focus,
  select:focus,
  textarea:focus {
    outline: none;
  }
}
```

---

### 5. DARK MODE SHADOW IMPROVEMENT
**File:** `tailwind.config.js` (lines 83-90)

**Current Code:**
```js
boxShadow: {
  '3xl': ' 0px 0px 20px 5px rgba(0, 0, 0, 0.05)',
  '4xl': [
    '0 35px 35px rgba(0, 0, 0, 0.25)',
    '0 45px 65px rgba(0, 0, 0, 0.15)',
  ],
  xl: ' 0px 6px 10px -4px rgba(0, 0, 0, 0.25);',
}
```

**Improved Code:**
```js
boxShadow: {
  '3xl': ' 0px 0px 20px 5px rgba(0, 0, 0, 0.05)',
  '3xl-dark': ' 0px 0px 20px 5px rgba(0, 0, 0, 0.3)',
  '4xl': [
    '0 35px 35px rgba(0, 0, 0, 0.25)',
    '0 45px 65px rgba(0, 0, 0, 0.15)',
  ],
  'xl': ' 0px 6px 10px -4px rgba(0, 0, 0, 0.25)',
  'sm-dark': ' 0px 2px 4px rgba(0, 0, 0, 0.5)',
  'md-dark': ' 0px 4px 8px rgba(0, 0, 0, 0.5)',
}
```

---

### 6. RESPONSIVE IMAGE COMPONENT
**File:** Create new file `components/ResponsiveImage.tsx`

```tsx
import Image, { ImageProps } from 'next/image';
import { ReactNode } from 'react';

interface ResponsiveImageProps extends Omit<ImageProps, 'src'> {
  src: string;
  fallback?: ReactNode;
  aspectRatio?: 'square' | 'video' | 'auto';
}

export const ResponsiveImage = ({
  src,
  alt,
  fallback,
  aspectRatio = 'auto',
  className = '',
  ...props
}: ResponsiveImageProps) => {
  const aspectClasses = {
    square: 'aspect-square',
    video: 'aspect-video',
    auto: '',
  };

  return (
    <div className={`relative w-full overflow-hidden rounded-lg ${aspectClasses[aspectRatio]}`}>
      <Image
        src={src}
        alt={alt}
        fill
        className={`object-cover ${className}`}
        sizes="(max-width: 640px) 100vw,
               (max-width: 1024px) 90vw,
               (max-width: 1280px) 80vw,
               1000px"
        priority={false}
        {...props}
      />
      {fallback && (
        <div className='absolute inset-0 flex items-center justify-center bg-gray-200 dark:bg-gray-700'>
          {fallback}
        </div>
      )}
    </div>
  );
};
```

---

### 7. LOADING SKELETON COMPONENT
**File:** Create new file `components/Skeleton.tsx`

```tsx
import React from 'react';

export const CardSkeleton = () => (
  <div className='p-6 rounded-lg border border-gray-200 dark:border-gray-700'>
    <div className='mb-4 h-6 w-3/4 rounded bg-gray-200 dark:bg-gray-700 animate-pulse'></div>
    <div className='space-y-2 mb-4'>
      <div className='h-4 rounded bg-gray-200 dark:bg-gray-700 animate-pulse'></div>
      <div className='h-4 w-5/6 rounded bg-gray-200 dark:bg-gray-700 animate-pulse'></div>
      <div className='h-4 w-4/6 rounded bg-gray-200 dark:bg-gray-700 animate-pulse'></div>
    </div>
    <div className='h-10 rounded bg-gray-200 dark:bg-gray-700 animate-pulse'></div>
  </div>
);

export const TextSkeleton = () => (
  <div className='space-y-3'>
    {[...Array(5)].map((_, i) => (
      <div
        key={i}
        className='h-4 rounded bg-gray-200 dark:bg-gray-700 animate-pulse'
        style={{ width: `${85 + Math.random() * 15}%` }}
      ></div>
    ))}
  </div>
);

export const ImageSkeleton = () => (
  <div className='w-full aspect-video rounded-lg bg-gray-200 dark:bg-gray-700 animate-pulse'></div>
);
```

---

### 8. CARD COMPONENT ENHANCEMENT
**File:** `components/Card.tsx` (replace current implementation)

**Current Issue:** Static design without variants or states

**Improved Implementation:**
```tsx
import React from 'react';
import Link from 'next/link';
import TextTruncate from 'react-text-truncate';
import Image from 'next/image';
import { Card as ShadcnCard } from '@/components/ui/card';
import { Separator } from '@/components/ui/separator';
import { cn } from '@/lib/utils';
import { CardSkeleton } from './Skeleton';

export interface CardProps {
  title: string;
  body: string;
  icon?: string;
  link?: string;
  image?: string;
  extended?: boolean;
  headerSize?: 'small' | 'medium' | 'large';
  bodyTextSize?: 'small' | 'medium' | 'large';
  variant?: 'elevated' | 'outlined' | 'flat';
  interactive?: boolean;
  loading?: boolean;
  error?: Error | null;
}

const CardBody = ({
  title,
  body,
  icon,
  link,
  image,
  extended,
  headerSize = 'medium',
  bodyTextSize = 'medium',
  variant = 'elevated',
  interactive = false,
  loading = false,
  error = null,
}: CardProps) => {
  if (loading) return <CardSkeleton />;
  if (error) return <div className='p-6 text-red-600'>Error loading card</div>;

  const headerSizeClasses = {
    small: 'text-sm',
    medium: 'text-lg',
    large: 'text-2xl',
  };

  const bodyTextSizeClasses = {
    small: 'text-sm',
    medium: 'text-base',
    large: 'text-lg',
  };

  const variantClasses = {
    elevated: 'shadow-lg dark:shadow-lg dark:shadow-slate-900/50 border border-gray-200 dark:border-gray-700',
    outlined: 'border-2 border-gray-200 dark:border-gray-700 shadow-sm',
    flat: 'bg-gray-50 dark:bg-gray-800 border border-gray-200 dark:border-gray-700',
  };

  return (
    <ShadcnCard
      className={cn(
        'group relative h-full w-full rounded-lg p-6 transition-all duration-300',
        interactive && 'hover:shadow-xl hover:scale-102 cursor-pointer',
        variantClasses[variant],
        'dark:bg-slate-800 hover:dark:bg-slate-700'
      )}
    >
      {image && (
        <div className='flex justify-center mb-4'>
          <Image
            src={image}
            alt={title}
            width={384}
            height={128}
            className='h-32 object-contain'
            loading="lazy"
          />
        </div>
      )}

      <div className='flex flex-row items-start gap-4'>
        {icon && (
          <span className='flex h-12 w-12 flex-shrink-0 items-center justify-center rounded-lg bg-blue-100 dark:bg-blue-900 px-3'>
            <Image
              src={icon}
              alt=""
              width={40}
              height={40}
              className='h-full w-full'
              loading="lazy"
            />
          </span>
        )}
        <h3 className={cn(
          'font-bold text-gray-900 dark:text-white',
          headerSizeClasses[headerSize]
        )}>
          {title}
        </h3>
      </div>

      <Separator className='my-4 bg-gray-300 dark:bg-gray-600' />

      <p className={cn(
        'mb-6 text-gray-700 dark:text-gray-200',
        bodyTextSizeClasses[bodyTextSize]
      )}>
        {extended ? (
          <span dangerouslySetInnerHTML={{ __html: body }} />
        ) : (
          <TextTruncate element='span' line={3} text={body} />
        )}
      </p>

      {link && (
        <p className='absolute bottom-4 right-4 font-medium text-primary opacity-0 transition-opacity delay-150 group-hover:opacity-100 group-focus-visible:opacity-100'>
          Read More →
        </p>
      )}
    </ShadcnCard>
  );
};

const Card: React.FC<CardProps> = ({ link, ...props }) => {
  return link ? (
    <Link href={link} className='block focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 rounded-lg'>
      <CardBody {...props} interactive={true} />
    </Link>
  ) : (
    <CardBody {...props} />
  );
};

export default Card;
```

---

### 9. TOAST NOTIFICATION SYSTEM
**File:** Create new file `components/Toast.tsx`

```tsx
import React, { useState, useCallback } from 'react';
import { createContext, useContext } from 'react';

type ToastType = 'success' | 'error' | 'info' | 'warning';

interface Toast {
  id: string;
  message: string;
  type: ToastType;
  duration?: number;
}

interface ToastContextType {
  toasts: Toast[];
  addToast: (message: string, type: ToastType, duration?: number) => void;
  removeToast: (id: string) => void;
}

const ToastContext = createContext<ToastContextType | undefined>(undefined);

export const ToastProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [toasts, setToasts] = useState<Toast[]>([]);

  const addToast = useCallback((message: string, type: ToastType, duration = 3000) => {
    const id = Date.now().toString();
    setToasts(prev => [...prev, { id, message, type, duration }]);

    if (duration) {
      setTimeout(() => removeToast(id), duration);
    }
  }, []);

  const removeToast = useCallback((id: string) => {
    setToasts(prev => prev.filter(toast => toast.id !== id));
  }, []);

  return (
    <ToastContext.Provider value={{ toasts, addToast, removeToast }}>
      {children}
      <ToastContainer toasts={toasts} onRemove={removeToast} />
    </ToastContext.Provider>
  );
};

export const useToast = () => {
  const context = useContext(ToastContext);
  if (!context) {
    throw new Error('useToast must be used within ToastProvider');
  }
  return context;
};

const ToastContainer: React.FC<{
  toasts: Toast[];
  onRemove: (id: string) => void;
}> = ({ toasts, onRemove }) => (
  <div className='fixed bottom-4 right-4 space-y-3 z-50'>
    {toasts.map(toast => (
      <Toast key={toast.id} {...toast} onRemove={onRemove} />
    ))}
  </div>
);

const Toast: React.FC<Toast & { onRemove: (id: string) => void }> = ({
  id,
  message,
  type,
  onRemove,
}) => {
  const bgColors = {
    success: 'bg-green-500',
    error: 'bg-red-500',
    info: 'bg-blue-500',
    warning: 'bg-yellow-500',
  };

  return (
    <div
      className={cn(
        'px-4 py-3 rounded-lg text-white shadow-lg animate-in fade-in slide-in-from-right',
        bgColors[type]
      )}
      role="alert"
    >
      <div className='flex items-center justify-between gap-4'>
        <span>{message}</span>
        <button
          onClick={() => onRemove(id)}
          className='hover:opacity-80 transition'
          aria-label="Close notification"
        >
          ✕
        </button>
      </div>
    </div>
  );
};
```

---

### 10. BREADCRUMB NAVIGATION COMPONENT
**File:** Create new file `components/Breadcrumb.tsx`

```tsx
import React from 'react';
import Link from 'next/link';
import { ChevronRight } from 'lucide-react';

interface BreadcrumbItem {
  label: string;
  href?: string;
}

interface BreadcrumbProps {
  items: BreadcrumbItem[];
}

export const Breadcrumb: React.FC<BreadcrumbProps> = ({ items }) => (
  <nav aria-label="Breadcrumb" className='mb-6'>
    <ol className='flex items-center gap-2 text-sm'>
      {items.map((item, index) => (
        <li key={index} className='flex items-center gap-2'>
          {index > 0 && (
            <ChevronRight className='w-4 h-4 text-gray-400' aria-hidden="true" />
          )}
          {item.href ? (
            <Link
              href={item.href}
              className='text-primary hover:underline transition-colors'
            >
              {item.label}
            </Link>
          ) : (
            <span className='text-gray-600 dark:text-gray-400'>{item.label}</span>
          )}
        </li>
      ))}
    </ol>
  </nav>
);
```

---

### 11. HERO BUTTON CONSOLIDATION
**File:** `pages/index.page.tsx` (lines 185-198)

**Current Code:**
```jsx
<Link
  href='/learn'
  className='flex items-center justify-center rounded border-2 border-white dark:border-none hover:bg-blue-700 transition-all duration-300 ease-in-out text-white w-[194px] h-[40px] font-semibold bg-primary dark:shadow-2xl'
>
  Getting started
</Link>
```

**Improved Code:**
```jsx
import { Button } from '@/components/ui/Button';

<Button
  asChild
  variant="primary"
  size="lg"
  className='border-2 border-white dark:border-transparent'
>
  <Link href='/learn'>
    Getting started
  </Link>
</Button>
```

---

### 12. MOBILE NAV ACCESSIBILITY
**File:** `components/Layout.tsx` (lines 299-328)

**Current Code:**
```jsx
const MobileNav = () => {
  const section = useContext(SectionContext);

  return (
    <div className='flex flex-col lg:hidden shadow-xl justify-end fixed bg-white w-full z-[190] top-16 left-0 pl-8 dark:bg-slate-800'>
      <MainNavLink .../>
    </div>
  );
};
```

**Improved Code:**
```jsx
const MobileNav = () => {
  const section = useContext(SectionContext);
  const mobileNavRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    // Trap focus within mobile nav
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Escape') {
        useStore.setState({ overlayNavigation: null });
      }
    };
    document.addEventListener('keydown', handleKeyDown);
    return () => document.removeEventListener('keydown', handleKeyDown);
  }, []);

  return (
    <nav
      id="mobile-nav"
      ref={mobileNavRef}
      className='flex flex-col lg:hidden shadow-xl justify-end fixed bg-white w-full z-[190] top-16 left-0 pl-8 dark:bg-slate-800'
      role="navigation"
      aria-label="Mobile navigation"
    >
      <MainNavLink .../>
    </nav>
  );
};
```

---

### 13. FOOTER RESPONSIVE FIX
**File:** `components/Layout.tsx` (lines 336-450)

**Current Issue:** h-[350px] on mobile is too tall

**Change:**
```jsx
<footer
  className={classnames(
    'z-10 h-auto md:h-[300px] bg-gradient-to-r from-startBlue from-1.95% to-endBlue dark:from-[#002C34] dark:to-[#023e8a] clip-top grid items-center py-8 md:py-4'
  )}
>
```

---

### 14. SPONSORED LOGOS RESPONSIVE
**File:** `pages/index.page.tsx` (lines 686-932)

**Current Code:**
```jsx
<div className='grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-12 items-center mx-auto md:mx-0 px-4'>
  <a href='...'>
    <Image src={asyncapi_logo} className=' w-44' width={176} height={100} alt='asyncapi' />
  </a>
```

**Improved Code:**
```jsx
<div className='grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-4 md:gap-6 lg:gap-8 items-center justify-center px-4'>
  <a href='...' className='flex items-center justify-center h-24'>
    <Image
      src={asyncapi_logo}
      alt='asyncapi logo'
      width={160}
      height={90}
      className='w-full h-auto max-h-20 object-contain'
    />
  </a>
```

---

### 15. HEADING LEVEL CONSISTENCY
**File:** Multiple files using inconsistent headings

**Create heading utility:**
```tsx
// components/Headlines.tsx
export const Heading = {
  H1: ({ children, className = '' }: { children: React.ReactNode; className?: string }) => (
    <h1 className={cn(
      'text-3xl md:text-5xl lg:text-6xl font-bold leading-tight tracking-tight',
      'text-gray-900 dark:text-white',
      className
    )}>
      {children}
    </h1>
  ),
  H2: ({ children, className = '' }: { children: React.ReactNode; className?: string }) => (
    <h2 className={cn(
      'text-2xl md:text-4xl lg:text-5xl font-bold leading-tight',
      'text-gray-900 dark:text-white',
      className
    )}>
      {children}
    </h2>
  ),
  H3: ({ children, className = '' }: { children: React.ReactNode; className?: string }) => (
    <h3 className={cn(
      'text-xl md:text-2xl lg:text-3xl font-semibold leading-snug',
      'text-gray-900 dark:text-white',
      className
    )}>
      {children}
    </h3>
  ),
  // H4, H5, H6...
};
```

---

### 16. COLOR PALETTE VARIABLES
**File:** `tailwind.config.js`

**Add semantic colors:**
```js
theme: {
  colors: {
    // Primary brand colors
    primary: '#002CC4',
    'primary-dark': '#001a8b',
    'primary-light': '#3d5eff',

    secondary: '#5468FF',
    accent: '#F47A08',

    // Semantic colors
    success: '#10B981',
    warning: '#F59E0B',
    error: '#EF4444',
    info: '#3B82F6',

    // Neutral scale (comprehensive)
    gray: {
      50: '#F9FAFB',
      100: '#F3F4F6',
      200: '#E5E7EB',
      300: '#D1D5DB',
      400: '#9CA3AF',
      500: '#6B7280',
      600: '#4B5563',
      700: '#374151',
      800: '#1F2937',
      900: '#111827',
    },
  }
}
```

---

### 17. HOME PAGE CONTENT STRUCTURE
**File:** `pages/index.page.tsx`

**Issue:** Too many sections competing for attention

**Recommendation:** Reorganize with clearer hierarchy:
1. Hero + CTA (Keep)
2. Why JSON Schema (Keep)
3. Feature cards (Keep, max 4)
4. CTA Section (Keep)
5. Community Showcase (Reduce size)
6. Sponsors/Media (Move below fold)
7. Footer CTA (Keep)

---

### 18. TOOLS PAGE FILTER UX
**File:** `pages/tools/components/Sidebar.tsx`

**Add applied filters summary:**
```tsx
const AppliedFilters = ({ filters, onClear }) => {
  const hasFilters = Object.values(filters).some(arr => arr && arr.length > 0);

  if (!hasFilters) return null;

  return (
    <div className='mb-4 p-4 bg-blue-50 dark:bg-blue-900/20 rounded-lg'>
      <div className='flex justify-between items-center mb-2'>
        <span className='text-sm font-semibold'>Active Filters</span>
        <button
          onClick={() => onClear()}
          className='text-xs text-primary hover:underline'
        >
          Clear All
        </button>
      </div>
      <div className='flex flex-wrap gap-2'>
        {Object.entries(filters).map(([key, values]) =>
          values?.map(value => (
            <Badge
              key={`${key}-${value}`}
              onRemove={() => onClear(key, value)}
            >
              {value}
            </Badge>
          ))
        )}
      </div>
    </div>
  );
};
```

---

### 19. ERROR BOUNDARY IMPLEMENTATION
**File:** Create new file `components/ErrorBoundary.tsx`

```tsx
import React from 'react';

interface Props {
  children: React.ReactNode;
  fallback?: React.ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('ErrorBoundary caught an error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        this.props.fallback || (
          <div className='p-6 bg-red-50 dark:bg-red-900/20 rounded-lg border border-red-200 dark:border-red-800'>
            <h2 className='font-bold text-red-800 mb-2'>Something went wrong</h2>
            <p className='text-red-700 text-sm'>{this.state.error?.message}</p>
          </div>
        )
      );
    }

    return this.props.children;
  }
}
```

---

### 20. ANIMATION & TRANSITION SYSTEM
**File:** `styles/globals.css`

**Add smooth transitions:**
```css
@layer components {
  /* Smooth color transitions */
  .transition-smooth {
    @apply transition-all duration-200 ease-in-out;
  }

  /* Hover elevation */
  .hover-lift {
    @apply hover:shadow-lg hover:-translate-y-1 transition-all duration-200;
  }

  /* Focus state */
  .focus-visible {
    @apply focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2;
  }

  /* Fade in animation */
  @keyframes fade-in {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }

  .animate-fade-in {
    animation: fade-in 0.3s ease-in;
  }
}
```

---

## Implementation Priority

### Week 1 (Quick Wins)
- [ ] Add focus styles
- [ ] Fix link contrast
- [ ] Add aria-labels
- [ ] Mobile nav button accessibility
- [ ] Button system component

### Week 2 (Core Updates)
- [ ] Card component enhancement
- [ ] Toast notification system
- [ ] Breadcrumb navigation
- [ ] Error boundary
- [ ] Loading skeletons

### Week 3 (Polish & Testing)
- [ ] Responsive images
- [ ] Dark mode refinement
- [ ] Accessibility audit
- [ ] Performance testing
- [ ] Cross-browser testing

---

## Testing Checklist

- [ ] WCAG 2.1 AA compliance
- [ ] Screen reader testing (NVDA, JAWS)
- [ ] Keyboard-only navigation
- [ ] Mobile responsiveness (320px - 2560px)
- [ ] Dark mode across all pages
- [ ] Loading states and error states
- [ ] Cross-browser compatibility (Chrome, Firefox, Safari, Edge)
- [ ] Touch interactions on mobile
- [ ] Performance (Lighthouse score > 90)

---

## Tools & Resources

- **Accessibility Testing:** WAVE, Axe DevTools, NVDA
- **Design System:** Storybook, Chromatic
- **Performance:** Lighthouse, WebPageTest
- **Color Contrast:** Contrast Ratio, Color Blindness Simulator
- **Keyboard Testing:** Tab navigation, screen readers

---

## Next Steps

1. Create feature branch: `feature/ui-ux-improvements`
2. Implement components following priority list
3. Test thoroughly with accessibility tools
4. Create PR with detailed changelog
5. Gather team feedback
6. Deploy incrementally using feature flags
