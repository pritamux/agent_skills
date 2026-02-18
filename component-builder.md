# UI Component Builder

## Purpose

This skill provides comprehensive guidelines for building production-ready UI components with a focus on React/Next.js ecosystems. It covers component architecture, naming conventions, accessibility standards, testing approaches, and documentation.

## Core Principles

### 1. Composition Over Inheritance
- Build components that can be easily composed together
- Use slots/children patterns for flexibility
- Avoid deep component hierarchies

### 2. Accessibility First
- Every component must be keyboard accessible
- Screen reader support is mandatory
- Follow WCAG 2.1 Level AA guidelines
- Test with real assistive technologies

### 3. Performance by Default
- Lazy load when appropriate
- Memoize expensive computations
- Minimize re-renders
- Optimize bundle size

### 4. Type Safety
- Use TypeScript for all components
- Define clear, explicit prop types
- Leverage type inference where possible

## Component Architecture

### Basic Structure

```tsx
// ComponentName.tsx
import React, { forwardRef } from 'react';
import { cn } from '@/lib/utils';
import type { ComponentNameProps } from './ComponentName.types';
import { componentVariants } from './ComponentName.variants';

export const ComponentName = forwardRef<HTMLDivElement, ComponentNameProps>(
  (
    {
      children,
      variant = 'default',
      size = 'md',
      className,
      ...props
    },
    ref
  ) => {
    return (
      <div
        ref={ref}
        className={cn(componentVariants({ variant, size }), className)}
        {...props}
      >
        {children}
      </div>
    );
  }
);

ComponentName.displayName = 'ComponentName';
```

### TypeScript Interfaces

```typescript
// ComponentName.types.ts
import { VariantProps } from 'class-variance-authority';
import { componentVariants } from './ComponentName.variants';

// Base props interface
export interface ComponentNameProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof componentVariants> {
  /**
   * The content to render inside the component
   */
  children: React.ReactNode;
  
  /**
   * Custom CSS class to apply
   */
  className?: string;
  
  /**
   * Whether the component is disabled
   * @default false
   */
  isDisabled?: boolean;
  
  /**
   * Callback fired when component is clicked
   */
  onClick?: (event: React.MouseEvent<HTMLDivElement>) => void;
}

// Polymorphic component types (for components that can render as different elements)
export type PolymorphicComponentProps<E extends React.ElementType> = {
  as?: E;
} & Omit<React.ComponentPropsWithoutRef<E>, 'as'>;
```

### Variant Management

```typescript
// ComponentName.variants.ts
import { cva } from 'class-variance-authority';

export const componentVariants = cva(
  // Base styles - applied to all variants
  'inline-flex items-center justify-center font-medium transition-all focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2',
  {
    variants: {
      variant: {
        default: 'bg-primary-500 text-white hover:bg-primary-600',
        secondary: 'bg-secondary-500 text-white hover:bg-secondary-600',
        outline: 'border-2 border-primary-500 text-primary-500 hover:bg-primary-50',
        ghost: 'text-primary-500 hover:bg-primary-100',
        danger: 'bg-red-500 text-white hover:bg-red-600'
      },
      size: {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4 text-base',
        lg: 'h-12 px-6 text-lg'
      },
      rounded: {
        none: 'rounded-none',
        sm: 'rounded-sm',
        md: 'rounded-md',
        lg: 'rounded-lg',
        full: 'rounded-full'
      }
    },
    compoundVariants: [
      {
        variant: 'outline',
        size: 'sm',
        className: 'border-[1.5px]'
      }
    ],
    defaultVariants: {
      variant: 'default',
      size: 'md',
      rounded: 'md'
    }
  }
);
```

## Naming Conventions

### Component Names

```
✅ Good:
- Button
- Card
- NavigationMenu
- UserAvatar
- SearchInput

❌ Bad:
- button (not PascalCase)
- BTN (unclear abbreviation)
- TheButton (unnecessary article)
- MyButton (possessive prefix)
```

### Props Naming

```typescript
// Boolean props: use "is" or "has" prefix
isDisabled, isLoading, isActive, hasError, hasIcon

// Event handlers: use "on" prefix
onClick, onChange, onSubmit, onFocus, onBlur

// Callback props: use "on" prefix
onComplete, onSuccess, onError

// Render props: use "render" prefix
renderHeader, renderFooter, renderItem

// Size/variant props: use enum-style strings
variant: 'primary' | 'secondary' | 'outline'
size: 'sm' | 'md' | 'lg'
```

### File Naming

```
components/
├── Button/
│   ├── Button.tsx              # Main component
│   ├── Button.types.ts         # TypeScript types
│   ├── Button.variants.ts      # CVA variants
│   ├── Button.stories.tsx      # Storybook stories
│   ├── Button.test.tsx         # Jest/RTL tests
│   ├── Button.spec.ts          # E2E tests (Playwright)
│   ├── ButtonGroup.tsx         # Related sub-component
│   └── index.ts                # Public exports
```

## Composition Patterns

### Compound Components

```tsx
// Card.tsx
import { createContext, useContext } from 'react';

const CardContext = createContext<{ variant: string } | undefined>(undefined);

export const Card = ({ variant = 'default', children, ...props }) => (
  <CardContext.Provider value={{ variant }}>
    <div className={cn('rounded-lg border', cardVariants({ variant }))} {...props}>
      {children}
    </div>
  </CardContext.Provider>
);

export const CardHeader = ({ children, ...props }) => (
  <div className="px-6 py-4 border-b" {...props}>
    {children}
  </div>
);

export const CardContent = ({ children, ...props }) => (
  <div className="px-6 py-4" {...props}>
    {children}
  </div>
);

export const CardFooter = ({ children, ...props }) => (
  <div className="px-6 py-4 border-t" {...props}>
    {children}
  </div>
);

// Usage
<Card variant="elevated">
  <CardHeader>Title</CardHeader>
  <CardContent>Content here</CardContent>
  <CardFooter>Footer actions</CardFooter>
</Card>
```

### Render Props Pattern

```tsx
interface DataListProps<T> {
  data: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
  renderEmpty?: () => React.ReactNode;
  isLoading?: boolean;
}

export const DataList = <T,>({
  data,
  renderItem,
  renderEmpty = () => <p>No items found</p>,
  isLoading = false
}: DataListProps<T>) => {
  if (isLoading) {
    return <LoadingSpinner />;
  }
  
  if (data.length === 0) {
    return renderEmpty();
  }
  
  return (
    <ul>
      {data.map((item, index) => (
        <li key={index}>{renderItem(item, index)}</li>
      ))}
    </ul>
  );
};

// Usage
<DataList
  data={users}
  renderItem={(user) => <UserCard user={user} />}
  renderEmpty={() => <EmptyState message="No users found" />}
/>
```

### Slots Pattern

```tsx
interface LayoutProps {
  header?: React.ReactNode;
  sidebar?: React.ReactNode;
  content: React.ReactNode;
  footer?: React.ReactNode;
}

export const Layout = ({ header, sidebar, content, footer }: LayoutProps) => (
  <div className="min-h-screen flex flex-col">
    {header && <header className="border-b">{header}</header>}
    <div className="flex flex-1">
      {sidebar && <aside className="w-64 border-r">{sidebar}</aside>}
      <main className="flex-1 p-6">{content}</main>
    </div>
    {footer && <footer className="border-t">{footer}</footer>}
  </div>
);
```

## Accessibility Standards

### WCAG 2.1 Level AA Checklist

#### Perceivable
- [ ] **Color contrast**: Text has 4.5:1 ratio (3:1 for large text)
- [ ] **Alternative text**: Images have meaningful alt text
- [ ] **Adaptable**: Content can be presented in different ways

#### Operable
- [ ] **Keyboard accessible**: All functionality available via keyboard
- [ ] **Focus visible**: Clear focus indicators (focus-visible:ring-2)
- [ ] **No keyboard traps**: Users can navigate away from all elements
- [ ] **Skip links**: Provide skip to main content links

#### Understandable
- [ ] **Readable**: Language of page is specified (lang attribute)
- [ ] **Predictable**: Components behave consistently
- [ ] **Input assistance**: Clear labels and error messages

#### Robust
- [ ] **Semantic HTML**: Use correct HTML elements
- [ ] **ARIA**: Use ARIA attributes when semantic HTML isn't enough
- [ ] **Valid markup**: Ensure HTML is valid and well-formed

### Accessible Component Examples

#### Button

```tsx
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ children, isLoading, isDisabled, ...props }, ref) => (
    <button
      ref={ref}
      disabled={isDisabled || isLoading}
      aria-disabled={isDisabled}
      aria-busy={isLoading}
      {...props}
    >
      {isLoading && (
        <>
          <span className="sr-only">Loading...</span>
          <LoadingIcon aria-hidden="true" />
        </>
      )}
      {children}
    </button>
  )
);
```

#### Form Input

```tsx
export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, hint, required, ...props }, ref) => {
    const id = useId();
    const errorId = `${id}-error`;
    const hintId = `${id}-hint`;
    
    return (
      <div>
        <label htmlFor={id} className="block mb-2">
          {label}
          {required && <span aria-label="required"> *</span>}
        </label>
        
        {hint && (
          <p id={hintId} className="text-sm text-gray-600 mb-1">
            {hint}
          </p>
        )}
        
        <input
          ref={ref}
          id={id}
          aria-invalid={!!error}
          aria-describedby={`${error ? errorId : ''} ${hint ? hintId : ''}`.trim()}
          aria-required={required}
          {...props}
        />
        
        {error && (
          <p id={errorId} className="text-sm text-red-600 mt-1" role="alert">
            {error}
          </p>
        )}
      </div>
    );
  }
);
```

#### Dialog/Modal

```tsx
export const Dialog = ({ isOpen, onClose, title, children }) => {
  const titleId = useId();
  
  useEffect(() => {
    if (!isOpen) return;
    
    // Trap focus within dialog
    const previouslyFocused = document.activeElement as HTMLElement;
    
    // Handle Escape key
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') onClose();
    };
    
    document.addEventListener('keydown', handleEscape);
    
    return () => {
      document.removeEventListener('keydown', handleEscape);
      previouslyFocused?.focus();
    };
  }, [isOpen, onClose]);
  
  if (!isOpen) return null;
  
  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby={titleId}
      className="fixed inset-0 z-50 flex items-center justify-center"
    >
      {/* Backdrop */}
      <div
        className="fixed inset-0 bg-black/50"
        onClick={onClose}
        aria-hidden="true"
      />
      
      {/* Dialog content */}
      <div className="relative bg-white rounded-lg p-6 max-w-md">
        <h2 id={titleId} className="text-xl font-semibold mb-4">
          {title}
        </h2>
        {children}
      </div>
    </div>
  );
};
```

## Responsive Design

### Mobile-First Approach

```tsx
// Start with mobile styles, enhance for larger screens
const ResponsiveCard = () => (
  <div className="
    // Mobile (default)
    px-4
    py-3
    text-sm
    flex-col
    
    // Tablet (md: 768px+)
    md:px-6
    md:py-4
    md:text-base
    
    // Desktop (lg: 1024px+)
    lg:px-8
    lg:flex-row
    lg:items-center
  ">
    <div className="mb-4 lg:mb-0 lg:mr-4">
      <Image src="/avatar.jpg" alt="User" width={48} height={48} />
    </div>
    <div>
      <h3 className="font-semibold">Title</h3>
      <p className="text-gray-600">Description</p>
    </div>
  </div>
);
```

### Container Queries (Modern Approach)

```tsx
const ContainerCard = () => (
  <div className="@container">
    <div className="
      grid
      grid-cols-1
      @md:grid-cols-2
      @lg:grid-cols-3
      gap-4
    ">
      {/* Items */}
    </div>
  </div>
);
```

## Testing Strategy

### Unit Tests (Jest + React Testing Library)

```typescript
// Button.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

describe('Button', () => {
  it('renders with children', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });
  
  it('calls onClick when clicked', async () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await userEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  it('does not call onClick when disabled', async () => {
    const handleClick = jest.fn();
    render(<Button isDisabled onClick={handleClick}>Click me</Button>);
    
    await userEvent.click(screen.getByText('Click me'));
    expect(handleClick).not.toHaveBeenCalled();
  });
  
  it('is keyboard accessible', async () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    const button = screen.getByText('Click me');
    button.focus();
    expect(button).toHaveFocus();
    
    await userEvent.keyboard('{Enter}');
    expect(handleClick).toHaveBeenCalled();
  });
});
```

### Accessibility Tests

```typescript
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

describe('Button accessibility', () => {
  it('has no accessibility violations', async () => {
    const { container } = render(<Button>Click me</Button>);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
});
```

### Visual Regression Tests (Playwright)

```typescript
// Button.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Button visual tests', () => {
  test('primary variant matches snapshot', async ({ page }) => {
    await page.goto('/components/button');
    const button = page.locator('[data-testid="button-primary"]');
    await expect(button).toHaveScreenshot('button-primary.png');
  });
  
  test('hover state matches snapshot', async ({ page }) => {
    await page.goto('/components/button');
    const button = page.locator('[data-testid="button-primary"]');
    await button.hover();
    await expect(button).toHaveScreenshot('button-primary-hover.png');
  });
});
```

## Documentation Standards

### Component README Template

````markdown
# ComponentName

## Overview
Brief description of what this component does and when to use it.

## Installation

```bash
npm install @your-org/component-name
```

## Basic Usage

```tsx
import { ComponentName } from '@your-org/component-name';

function App() {
  return (
    <ComponentName variant="primary" size="md">
      Content here
    </ComponentName>
  );
}
```

## Props API

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | `'primary' \| 'secondary'` | `'primary'` | Visual style variant |
| size | `'sm' \| 'md' \| 'lg'` | `'md'` | Component size |
| isDisabled | `boolean` | `false` | Whether the component is disabled |
| children | `React.ReactNode` | - | Content to render |

## Examples

### Different Variants

```tsx
<ComponentName variant="primary">Primary</ComponentName>
<ComponentName variant="secondary">Secondary</ComponentName>
```

### With Icons

```tsx
<ComponentName>
  <Icon name="star" />
  With Icon
</ComponentName>
```

## Accessibility

- ✅ Keyboard accessible (Tab, Enter, Space)
- ✅ Screen reader friendly
- ✅ WCAG 2.1 Level AA compliant
- ✅ Focus visible

## Browser Support

- Chrome (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Edge (last 2 versions)

## Related Components

- [RelatedComponent1](#)
- [RelatedComponent2](#)
````

## Performance Optimization

### Memoization

```tsx
// Memoize expensive child components
const MemoizedChild = React.memo(ChildComponent, (prevProps, nextProps) => {
  // Custom comparison
  return prevProps.id === nextProps.id;
});

// Use useMemo for expensive calculations
const ExpensiveComponent = ({ data }) => {
  const processedData = useMemo(() => {
    return data.map(item => expensiveTransform(item));
  }, [data]);
  
  return <List items={processedData} />;
};

// Use useCallback for event handlers
const Parent = () => {
  const handleClick = useCallback((id: string) => {
    console.log('Clicked:', id);
  }, []);
  
  return <Child onClick={handleClick} />;
};
```

### Code Splitting

```tsx
// Lazy load heavy components
const HeavyChart = lazy(() => import('./HeavyChart'));

function Dashboard() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <HeavyChart data={data} />
    </Suspense>
  );
}
```

## Validation Criteria

A component is production-ready when:

- [ ] **Functionality**: All features work as expected
- [ ] **Types**: Full TypeScript coverage with no `any` types
- [ ] **Accessibility**: Passes axe and manual testing
- [ ] **Tests**: 80%+ code coverage
- [ ] **Documentation**: Complete README and Storybook stories
- [ ] **Performance**: No unnecessary re-renders
- [ ] **Responsive**: Works on mobile, tablet, and desktop
- [ ] **Browser support**: Works in all target browsers
- [ ] **Design approval**: Matches design specifications
- [ ] **Code review**: Approved by at least one other developer

---

**Last Updated**: 2026-02-18
**Version**: 1.0.0