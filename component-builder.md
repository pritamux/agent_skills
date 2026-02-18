# UI Component Builder

> Comprehensive skill file for building production-ready UI components with focus on architecture, accessibility, testing, and documentation

## Overview

This skill provides a systematic approach to building reusable, accessible, and well-tested UI components. It covers architecture decisions, naming conventions, accessibility standards, comprehensive testing strategies, and documentation practices that ensure components are maintainable and developer-friendly.

**Target Role**: Frontend Developer, UI Engineer, Component Library Maintainer  
**Scope**: Component design, implementation, testing, and documentation  
**Output**: Production-ready, accessible, well-documented UI components

## Core Principles

### 1. Composability
- Build small, focused components that do one thing well
- Compose complex UIs from simple, reusable building blocks
- Avoid monolithic components with too many responsibilities

### 2. Accessibility by Default
- Every component must be usable by everyone
- Follow WCAG 2.1 Level AA standards minimum
- Test with keyboard, screen readers, and assistive technologies

### 3. API Design
- Intuitive and consistent prop interfaces
- Sensible defaults that work for 80% of use cases
- Flexibility for advanced use cases

### 4. Performance
- Optimize for rendering performance
- Lazy load when appropriate
- Minimize bundle size impact

### 5. Developer Experience
- Clear, comprehensive documentation
- Helpful error messages
- TypeScript support with accurate types

## Component Architecture

### Directory Structure
```
components/
├── Button/
│   ├── Button.tsx           # Main component
│   ├── Button.types.ts      # TypeScript types
│   ├── Button.styles.ts     # Styles (CSS-in-JS or class variants)
│   ├── Button.test.tsx      # Unit tests
│   ├── Button.stories.tsx   # Storybook stories
│   ├── Button.spec.tsx      # E2E/integration tests
│   ├── index.ts            # Public exports
│   └── README.md           # Component documentation
├── Input/
│   ├── Input.tsx
│   ├── Input.types.ts
│   └── ...
└── ...
```

### Component Anatomy

#### 1. Types Definition (Component.types.ts)
```typescript
import { ComponentPropsWithoutRef, ReactNode } from 'react';

/**
 * Base props that extend native HTML button attributes
 */
export interface ButtonProps extends ComponentPropsWithoutRef<'button'> {
  /**
   * Visual style variant of the button
   * @default 'primary'
   */
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost' | 'danger';

  /**
   * Size of the button
   * @default 'md'
   */
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl';

  /**
   * Disabled state - prevents interaction
   * @default false
   */
  disabled?: boolean;

  /**
   * Loading state - shows spinner and prevents interaction
   * @default false
   */
  loading?: boolean;

  /**
   * Makes button full width
   * @default false
   */
  fullWidth?: boolean;

  /**
   * Icon element to display before children
   */
  leftIcon?: ReactNode;

  /**
   * Icon element to display after children
   */
  rightIcon?: ReactNode;

  /**
   * Content of the button
   */
  children: ReactNode;
}

/**
 * Internal component state (if needed)
 */
export interface ButtonState {
  isFocused: boolean;
  isPressed: boolean;
}
```

#### 2. Styles (Component.styles.ts)
```typescript
import { cva, type VariantProps } from 'class-variance-authority';

/**
 * Button style variants using CVA (Class Variance Authority)
 */
export const buttonStyles = cva(
  // Base styles applied to all variants
  [
    'inline-flex items-center justify-center',
    'font-medium rounded-lg',
    'transition-all duration-200',
    'focus:outline-none focus:ring-2 focus:ring-offset-2',
    'disabled:opacity-50 disabled:cursor-not-allowed disabled:pointer-events-none',
  ],
  {
    variants: {
      variant: {
        primary: [
          'bg-blue-600 text-white',
          'hover:bg-blue-700',
          'active:bg-blue-800',
          'focus:ring-blue-500',
        ],
        secondary: [
          'bg-gray-600 text-white',
          'hover:bg-gray-700',
          'active:bg-gray-800',
          'focus:ring-gray-500',
        ],
        outline: [
          'border-2 border-gray-300 text-gray-700 bg-transparent',
          'hover:border-gray-400 hover:bg-gray-50',
          'active:bg-gray-100',
          'focus:ring-gray-500',
        ],
        ghost: [
          'text-gray-700 bg-transparent',
          'hover:bg-gray-100',
          'active:bg-gray-200',
          'focus:ring-gray-500',
        ],
        danger: [
          'bg-red-600 text-white',
          'hover:bg-red-700',
          'active:bg-red-800',
          'focus:ring-red-500',
        ],
      },
      size: {
        xs: 'text-xs px-2 py-1 gap-1',
        sm: 'text-sm px-3 py-1.5 gap-1.5',
        md: 'text-base px-4 py-2 gap-2',
        lg: 'text-lg px-5 py-2.5 gap-2.5',
        xl: 'text-xl px-6 py-3 gap-3',
      },
      fullWidth: {
        true: 'w-full',
        false: 'w-auto',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
      fullWidth: false,
    },
  }
);

export type ButtonStylesProps = VariantProps<typeof buttonStyles>;

/**
 * Icon wrapper styles
 */
export const iconStyles = cva('flex-shrink-0', {
  variants: {
    size: {
      xs: 'w-3 h-3',
      sm: 'w-4 h-4',
      md: 'w-5 h-5',
      lg: 'w-6 h-6',
      xl: 'w-7 h-7',
    },
  },
});
```

#### 3. Component Implementation (Component.tsx)
```typescript
import React, { forwardRef } from 'react';
import { buttonStyles, iconStyles } from './Button.styles';
import { ButtonProps } from './Button.types';
import { Spinner } from '../Spinner';

/**
 * Button component - Primary action trigger
 * 
 * @example
 * ```tsx
 * <Button variant="primary" onClick={handleClick}>
 *   Click me
 * </Button>
 * ```
 */
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      variant = 'primary',
      size = 'md',
      disabled = false,
      loading = false,
      fullWidth = false,
      leftIcon,
      rightIcon,
      children,
      className,
      onClick,
      type = 'button',
      ...props
    },
    ref
  ) => {
    // Combine disabled states
    const isDisabled = disabled || loading;

    // Handle click with loading state
    const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
      if (loading || disabled) {
        event.preventDefault();
        return;
      }
      onClick?.(event);
    };

    return (
      <button
        ref={ref}
        type={type}
        className={buttonStyles({ variant, size, fullWidth, className })}
        disabled={isDisabled}
        onClick={handleClick}
        aria-busy={loading}
        aria-disabled={isDisabled}
        {...props}
      >
        {/* Loading spinner */}
        {loading && (
          <Spinner
            size={size}
            className={iconStyles({ size })}
            aria-label="Loading"
          />
        )}

        {/* Left icon */}
        {leftIcon && !loading && (
          <span className={iconStyles({ size })} aria-hidden="true">
            {leftIcon}
          </span>
        )}

        {/* Button text */}
        <span>{children}</span>

        {/* Right icon */}
        {rightIcon && (
          <span className={iconStyles({ size })} aria-hidden="true">
            {rightIcon}
          </span>
        )}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

#### 4. Public Exports (index.ts)
```typescript
export { Button } from './Button';
export type { ButtonProps } from './Button.types';
export { buttonStyles } from './Button.styles';
```

## Naming Conventions

### Component Names
```typescript
// ✅ Good: Clear, descriptive, PascalCase
Button
TextField
DropdownMenu
NavigationBar
ProductCard

// ❌ Bad: Unclear, abbreviated
Btn
TF
Dropdown  // Too generic
NavBar    // Abbreviated
Card      // Too generic
```

### Props Naming
```typescript
// ✅ Good: Clear intent, consistent patterns
interface ComponentProps {
  isDisabled: boolean;      // Boolean with 'is', 'has', 'should'
  hasError: boolean;
  shouldAutoFocus: boolean;
  
  onSubmit: () => void;     // Event handlers with 'on' prefix
  onClick: () => void;
  onChange: (value: string) => void;
  
  variant: 'primary';       // Enums without prefix
  size: 'large';
  
  leftIcon: ReactNode;      // Position descriptors
  rightIcon: ReactNode;
}

// ❌ Bad: Inconsistent, unclear
interface ComponentProps {
  disabled: boolean;        // Missing 'is' prefix
  error: boolean;           // Missing 'has' prefix
  
  submit: () => void;       // Missing 'on' prefix
  clickHandler: () => void; // Inconsistent pattern
  
  buttonVariant: 'primary'; // Redundant prefix
  btnSize: 'large';         // Abbreviated
}
```

### File Naming
```
// ✅ Good: Consistent, descriptive
Button.tsx
Button.types.ts
Button.styles.ts
Button.test.tsx
Button.stories.tsx

// ❌ Bad: Inconsistent patterns
button.tsx
ButtonTypes.ts
buttonstyles.ts
button.spec.tsx
Button.story.tsx
```

## Accessibility Standards

### WCAG 2.1 Level AA Requirements

#### 1. Keyboard Navigation
```typescript
const AccessibleButton = () => {
  return (
    <button
      onKeyDown={(e) => {
        // Space and Enter should trigger button
        if (e.key === ' ' || e.key === 'Enter') {
          e.preventDefault();
          handleClick();
        }
      }}
      tabIndex={0}  // Ensure it's in tab order
    >
      Action
    </button>
  );
};

const AccessibleList = () => {
  return (
    <ul
      role="listbox"
      onKeyDown={(e) => {
        // Arrow key navigation
        if (e.key === 'ArrowDown') {
          focusNextItem();
        } else if (e.key === 'ArrowUp') {
          focusPreviousItem();
        }
      }}
    >
      {items.map((item) => (
        <li key={item.id} role="option" tabIndex={0}>
          {item.label}
        </li>
      ))}
    </ul>
  );
};
```

#### 2. ARIA Attributes
```typescript
// Labels and descriptions
<button
  aria-label="Close dialog"              // When no visible text
  aria-labelledby="dialog-title"         // Reference to label element
  aria-describedby="dialog-description"  // Additional context
>
  <Icon name="close" aria-hidden="true" />  {/* Decorative */}
</button>

// States
<button
  aria-pressed={isPressed}      // Toggle button state
  aria-expanded={isExpanded}    // Expandable content
  aria-disabled={isDisabled}    // Disabled state
  aria-busy={isLoading}         // Loading state
  aria-current="page"           // Current navigation item
>

// Live regions
<div
  role="alert"                  // Immediate announcement
  aria-live="polite"           // Announce when convenient
  aria-atomic="true"           // Read entire content
>
  {statusMessage}
</div>

// Complex widgets
<div
  role="dialog"                 // Dialog role
  aria-modal="true"            // Modal dialog
  aria-labelledby="dialog-title"
>
  <h2 id="dialog-title">Confirm Action</h2>
</div>
```

#### 3. Color Contrast
```typescript
// Minimum contrast ratios:
// - Normal text: 4.5:1
// - Large text (18px+ or 14px+ bold): 3:1
// - UI components and graphics: 3:1

const colorPairs = {
  // ✅ Good: 7.0:1 contrast
  text: '#1F2937',      // Gray 800
  background: '#FFFFFF',
  
  // ✅ Good: 4.6:1 contrast
  linkText: '#2563EB',  // Blue 600
  linkBg: '#FFFFFF',
  
  // ❌ Bad: 2.9:1 contrast (fails WCAG AA)
  lightText: '#9CA3AF', // Gray 400
  lightBg: '#FFFFFF',
};

// Use tools to verify:
// - WebAIM Contrast Checker
// - Chrome DevTools
// - Axe DevTools
```

#### 4. Focus Management
```typescript
const Modal = ({ isOpen, onClose, children }) => {
  const modalRef = useRef<HTMLDivElement>(null);
  const previousFocusRef = useRef<HTMLElement | null>(null);

  useEffect(() => {
    if (isOpen) {
      // Save current focus
      previousFocusRef.current = document.activeElement as HTMLElement;
      
      // Move focus to modal
      modalRef.current?.focus();
      
      // Trap focus within modal
      const handleTab = (e: KeyboardEvent) => {
        if (e.key === 'Tab') {
          const focusableElements = modalRef.current?.querySelectorAll(
            'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
          );
          const firstElement = focusableElements?.[0] as HTMLElement;
          const lastElement = focusableElements?.[focusableElements.length - 1] as HTMLElement;

          if (e.shiftKey && document.activeElement === firstElement) {
            e.preventDefault();
            lastElement?.focus();
          } else if (!e.shiftKey && document.activeElement === lastElement) {
            e.preventDefault();
            firstElement?.focus();
          }
        }
      };

      document.addEventListener('keydown', handleTab);
      return () => document.removeEventListener('keydown', handleTab);
    } else {
      // Restore focus when modal closes
      previousFocusRef.current?.focus();
    }
  }, [isOpen]);

  return (
    <div
      ref={modalRef}
      role="dialog"
      aria-modal="true"
      tabIndex={-1}
    >
      {children}
    </div>
  );
};
```

#### 5. Touch Targets
```css
/* Minimum touch target size: 44x44px (iOS) / 48x48px (Android) */
.button {
  min-width: 44px;
  min-height: 44px;
  padding: 12px 16px;
}

/* For smaller visual buttons, add transparent padding */
.icon-button {
  width: 24px;   /* Visual size */
  height: 24px;
  padding: 10px; /* Total: 44x44px touch target */
}
```

### Accessibility Testing Checklist
```markdown
- [ ] Keyboard navigation works (Tab, Shift+Tab, Arrow keys, Enter, Space, Esc)
- [ ] Focus indicators clearly visible
- [ ] Screen reader announces all content correctly (test with NVDA/JAWS/VoiceOver)
- [ ] Color contrast meets WCAG AA standards (4.5:1 for text, 3:1 for UI)
- [ ] Touch targets minimum 44x44px on mobile
- [ ] ARIA attributes used correctly
- [ ] No keyboard traps
- [ ] Form labels properly associated
- [ ] Error messages clearly announced
- [ ] Loading/busy states communicated
- [ ] Works with browser zoom up to 200%
- [ ] Works with Windows High Contrast mode
```

## Testing Strategy

### 1. Unit Tests (Component.test.tsx)
```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

describe('Button', () => {
  describe('Rendering', () => {
    it('renders children correctly', () => {
      render(<Button>Click me</Button>);
      expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument();
    });

    it('applies variant styles', () => {
      const { rerender } = render(<Button variant="primary">Primary</Button>);
      const button = screen.getByRole('button');
      expect(button).toHaveClass('bg-blue-600');

      rerender(<Button variant="danger">Danger</Button>);
      expect(button).toHaveClass('bg-red-600');
    });

    it('renders with icons', () => {
      render(
        <Button
          leftIcon={<span data-testid="left-icon">←</span>}
          rightIcon={<span data-testid="right-icon">→</span>}
        >
          Text
        </Button>
      );
      expect(screen.getByTestId('left-icon')).toBeInTheDocument();
      expect(screen.getByTestId('right-icon')).toBeInTheDocument();
    });
  });

  describe('Interaction', () => {
    it('calls onClick when clicked', async () => {
      const handleClick = jest.fn();
      render(<Button onClick={handleClick}>Click</Button>);
      
      await userEvent.click(screen.getByRole('button'));
      expect(handleClick).toHaveBeenCalledTimes(1);
    });

    it('does not call onClick when disabled', async () => {
      const handleClick = jest.fn();
      render(<Button disabled onClick={handleClick}>Click</Button>);
      
      await userEvent.click(screen.getByRole('button'));
      expect(handleClick).not.toHaveBeenCalled();
    });

    it('does not call onClick when loading', async () => {
      const handleClick = jest.fn();
      render(<Button loading onClick={handleClick}>Click</Button>);
      
      await userEvent.click(screen.getByRole('button'));
      expect(handleClick).not.toHaveBeenCalled();
    });
  });

  describe('Accessibility', () => {
    it('is keyboard accessible', async () => {
      const handleClick = jest.fn();
      render(<Button onClick={handleClick}>Click</Button>);
      
      const button = screen.getByRole('button');
      button.focus();
      expect(button).toHaveFocus();
      
      await userEvent.keyboard('{Enter}');
      expect(handleClick).toHaveBeenCalled();
    });

    it('has correct ARIA attributes when loading', () => {
      render(<Button loading>Loading</Button>);
      const button = screen.getByRole('button');
      expect(button).toHaveAttribute('aria-busy', 'true');
      expect(button).toHaveAttribute('aria-disabled', 'true');
    });

    it('meets accessibility standards', async () => {
      const { container } = render(<Button>Accessible Button</Button>);
      const results = await axe(container);
      expect(results).toHaveNoViolations();
    });
  });

  describe('States', () => {
    it('shows loading spinner when loading', () => {
      render(<Button loading>Submit</Button>);
      expect(screen.getByRole('button')).toHaveAttribute('aria-busy', 'true');
    });

    it('applies disabled styling when disabled', () => {
      render(<Button disabled>Disabled</Button>);
      const button = screen.getByRole('button');
      expect(button).toBeDisabled();
      expect(button).toHaveClass('disabled:opacity-50');
    });
  });
});
```

### 2. Integration Tests (Component.spec.tsx)
```typescript
import { test, expect } from '@playwright/test';

test.describe('Button Component', () => {
  test('renders and is interactive', async ({ page }) => {
    await page.goto('/components/button');
    
    const button = page.getByRole('button', { name: 'Click me' });
    await expect(button).toBeVisible();
    
    await button.click();
    await expect(page.getByText('Button clicked!')).toBeVisible();
  });

  test('keyboard navigation works', async ({ page }) => {
    await page.goto('/components/button');
    
    await page.keyboard.press('Tab');
    const button = page.getByRole('button').first();
    await expect(button).toBeFocused();
    
    await page.keyboard.press('Enter');
    await expect(page.getByText('Button clicked!')).toBeVisible();
  });

  test('works on mobile viewport', async ({ page }) => {
    await page.setViewportSize({ width: 375, height: 667 });
    await page.goto('/components/button');
    
    const button = page.getByRole('button');
    const box = await button.boundingBox();
    
    // Verify touch target size
    expect(box?.width).toBeGreaterThanOrEqual(44);
    expect(box?.height).toBeGreaterThanOrEqual(44);
  });
});
```

### 3. Visual Regression Tests
```typescript
// Using Chromatic or Percy
import { test } from '@playwright/test';
import percySnapshot from '@percy/playwright';

test.describe('Button Visual Tests', () => {
  test('captures all button variants', async ({ page }) => {
    await page.goto('/components/button');
    await percySnapshot(page, 'Button - All Variants');
  });

  test('captures button states', async ({ page }) => {
    await page.goto('/components/button?states=true');
    await percySnapshot(page, 'Button - States');
  });

  test('captures dark mode', async ({ page }) => {
    await page.goto('/components/button');
    await page.emulateMedia({ colorScheme: 'dark' });
    await percySnapshot(page, 'Button - Dark Mode');
  });
});
```

## Documentation Standards

### 1. Component README (Component/README.md)
```markdown
# Button

Primary action trigger component with multiple variants and states.

## Usage

```tsx
import { Button } from '@/components/Button';

function App() {
  return (
    <Button variant="primary" onClick={handleClick}>
      Click me
    </Button>
  );
}
```

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'outline' \| 'ghost' \| 'danger'` | `'primary'` | Visual style variant |
| `size` | `'xs' \| 'sm' \| 'md' \| 'lg' \| 'xl'` | `'md'` | Size of the button |
| `disabled` | `boolean` | `false` | Disables interaction |
| `loading` | `boolean` | `false` | Shows loading spinner |
| `fullWidth` | `boolean` | `false` | Makes button full width |
| `leftIcon` | `ReactNode` | - | Icon before text |
| `rightIcon` | `ReactNode` | - | Icon after text |
| `onClick` | `(event: MouseEvent) => void` | - | Click handler |
| `children` | `ReactNode` | - | Button content |

## Examples

### Basic Button
```tsx
<Button>Default Button</Button>
```

### With Icons
```tsx
<Button leftIcon={<PlusIcon />}>Add Item</Button>
<Button rightIcon={<ArrowRightIcon />}>Continue</Button>
```

### Loading State
```tsx
<Button loading>Submitting...</Button>
```

### Full Width
```tsx
<Button fullWidth>Full Width Button</Button>
```

## Accessibility

- Keyboard accessible (Enter/Space to activate)
- ARIA attributes for loading and disabled states
- Minimum touch target size: 44x44px
- Focus indicator visible
- Color contrast meets WCAG AA standards

## Best Practices

### Do ✅
- Use semantic button text that describes the action
- Use appropriate variants (danger for destructive actions)
- Include loading states for async operations
- Provide feedback after button click

### Don't ❌
- Use "Click here" as button text
- Create buttons without click handlers
- Nest interactive elements inside buttons
- Use buttons for navigation (use links instead)
```

### 2. Storybook Documentation (Component.stories.tsx)
```typescript
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';
import { PlusIcon, ArrowRightIcon } from '@icons';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  parameters: {
    layout: 'centered',
    docs: {
      description: {
        component: 'Primary action trigger with multiple variants and states. Fully accessible and keyboard navigable.',
      },
    },
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'outline', 'ghost', 'danger'],
      description: 'Visual style variant',
    },
    size: {
      control: 'select',
      options: ['xs', 'sm', 'md', 'lg', 'xl'],
      description: 'Size of the button',
    },
    onClick: { action: 'clicked' },
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

// Default story
export const Default: Story = {
  args: {
    children: 'Button',
  },
};

// All variants
export const Variants: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button variant="primary">Primary</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="danger">Danger</Button>
    </div>
  ),
};

// All sizes
export const Sizes: Story = {
  render: () => (
    <div className="flex items-center gap-4">
      <Button size="xs">Extra Small</Button>
      <Button size="sm">Small</Button>
      <Button size="md">Medium</Button>
      <Button size="lg">Large</Button>
      <Button size="xl">Extra Large</Button>
    </div>
  ),
};

// With icons
export const WithIcons: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button leftIcon={<PlusIcon />}>Add Item</Button>
      <Button rightIcon={<ArrowRightIcon />}>Continue</Button>
    </div>
  ),
};

// States
export const States: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button>Default</Button>
      <Button disabled>Disabled</Button>
      <Button loading>Loading</Button>
    </div>
  ),
};

// Full width
export const FullWidth: Story = {
  args: {
    fullWidth: true,
    children: 'Full Width Button',
  },
  parameters: {
    layout: 'padded',
  },
};
```

### 3. JSDoc Comments
```typescript
/**
 * Button component for triggering actions and events.
 * 
 * @component
 * @example
 * ```tsx
 * <Button variant="primary" onClick={handleSubmit}>
 *   Submit Form
 * </Button>
 * ```
 * 
 * @example
 * ```tsx
 * // With loading state
 * <Button loading={isSubmitting}>
 *   {isSubmitting ? 'Submitting...' : 'Submit'}
 * </Button>
 * ```
 */
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(...);
```

## Best Practices

### 1. Component Composition
```typescript
// ✅ Good: Small, composable components
const Card = ({ children }) => <div className="card">{children}</div>;
const CardHeader = ({ children }) => <header>{children}</header>;
const CardBody = ({ children }) => <div>{children}</div>;
const CardFooter = ({ children }) => <footer>{children}</footer>;

// Usage
<Card>
  <CardHeader>Title</CardHeader>
  <CardBody>Content</CardBody>
  <CardFooter>Actions</CardFooter>
</Card>

// ❌ Bad: Monolithic component
const Card = ({ title, content, footer, showHeader, showFooter, ... }) => {
  return (
    <div>
      {showHeader && <header>{title}</header>}
      <div>{content}</div>
      {showFooter && <footer>{footer}</footer>}
    </div>
  );
};
```

### 2. Prop Defaults
```typescript
// ✅ Good: Sensible defaults for common use case
interface ButtonProps {
  variant?: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
}

const Button = ({ variant = 'primary', size = 'md' }) => {...};

// ❌ Bad: No defaults, forcing users to specify everything
const Button = ({ variant, size }: { variant: string; size: string }) => {...};
```

### 3. Performance Optimization
```typescript
// ✅ Good: Memoize expensive operations
import { memo, useMemo } from 'react';

const ExpensiveList = memo(({ items }) => {
  const sortedItems = useMemo(() => {
    return items.sort((a, b) => a.value - b.value);
  }, [items]);

  return <ul>{sortedItems.map(item => <li key={item.id}>{item.name}</li>)}</ul>;
});

// ❌ Bad: Recalculating on every render
const ExpensiveList = ({ items }) => {
  const sortedItems = items.sort((a, b) => a.value - b.value);
  return <ul>{sortedItems.map(item => <li key={item.id}>{item.name}</li>)}</ul>;
};
```

## Quality Checklist

Before considering a component complete:

### Implementation
- [ ] Component follows established architecture
- [ ] Props are well-typed with TypeScript
- [ ] Sensible defaults provided
- [ ] Styles use design tokens
- [ ] Performance optimized (memo, useMemo, useCallback where appropriate)
- [ ] Error handling implemented
- [ ] Loading states implemented

### Accessibility
- [ ] WCAG 2.1 AA compliant
- [ ] Keyboard navigation works
- [ ] ARIA attributes correct
- [ ] Focus management proper
- [ ] Color contrast verified
- [ ] Screen reader tested
- [ ] Touch targets adequate (44x44px min)

### Testing
- [ ] Unit tests written (>80% coverage)
- [ ] Integration tests for complex interactions
- [ ] Accessibility tests pass (axe)
- [ ] Visual regression tests configured
- [ ] Edge cases tested
- [ ] Error states tested

### Documentation
- [ ] README.md with examples
- [ ] Storybook stories created
- [ ] JSDoc comments complete
- [ ] Props table accurate
- [ ] Usage examples clear
- [ ] Best practices documented

### Code Quality
- [ ] Linting passes
- [ ] Type checking passes
- [ ] No console errors/warnings
- [ ] Follows team conventions
- [ ] Code reviewed

## Resources

### Tools
- **Testing Library**: `@testing-library/react`
- **Accessibility**: `axe-core`, `@axe-core/react`
- **Styling**: `tailwindcss`, `class-variance-authority`
- **Documentation**: `Storybook`
- **Type Safety**: `TypeScript`

### References
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [Testing Library Docs](https://testing-library.com/docs/react-testing-library/intro/)

---

**Skill Version**: 1.0.0  
**Last Updated**: February 2026  
**Maintained By**: Agent Skills Repository