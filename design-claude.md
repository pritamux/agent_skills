# Design Engineering Workflow

## Purpose

This skill enables AI agents to effectively bridge design and development by following systematic design engineering workflows. It covers design system integration, component creation, design-to-code translation, and collaborative design processes.

## Core Principles

### 1. Design System First
- Always reference the existing design system before creating new components
- Maintain consistency with established patterns and tokens
- Contribute back to the design system when creating reusable patterns

### 2. Atomic Design Methodology
- Think in terms of atoms → molecules → organisms → templates → pages
- Build small, composable pieces that combine into larger patterns
- Ensure each level maintains single responsibility

### 3. Design Tokens as Source of Truth
- Use tokens for colors, spacing, typography, and other design decisions
- Never hardcode values that should be tokenized
- Keep tokens synchronized between design and code

### 4. Accessibility by Default
- WCAG 2.1 Level AA compliance is mandatory
- Consider keyboard navigation, screen readers, and color contrast
- Test with assistive technologies

## Design-to-Development Workflow

### Phase 1: Design Analysis

**Checklist:**
- [ ] Review design files (Figma, Sketch, etc.)
- [ ] Identify all components and their states
- [ ] Map design tokens to code variables
- [ ] Note interaction patterns and animations
- [ ] Check responsive behavior across breakpoints
- [ ] Validate accessibility annotations

**Questions to Ask:**
1. What existing components can be reused?
2. Are there any new patterns that should be added to the design system?
3. What are the component's possible states? (default, hover, active, disabled, error, loading)
4. How does this component behave on mobile vs. desktop?
5. Are there any edge cases? (very long text, empty states, error states)

### Phase 2: Design Token Extraction

**Extract and document:**

```javascript
// colors.tokens.js
export const colors = {
  primary: {
    50: '#f0f9ff',
    100: '#e0f2fe',
    500: '#0ea5e9',
    600: '#0284c7',
    900: '#0c4a6e'
  },
  semantic: {
    success: '#10b981',
    error: '#ef4444',
    warning: '#f59e0b',
    info: '#3b82f6'
  }
};

// spacing.tokens.js
export const spacing = {
  xs: '0.25rem',  // 4px
  sm: '0.5rem',   // 8px
  md: '1rem',     // 16px
  lg: '1.5rem',   // 24px
  xl: '2rem',     // 32px
  '2xl': '3rem'   // 48px
};

// typography.tokens.js
export const typography = {
  fontFamily: {
    sans: 'Inter, system-ui, sans-serif',
    mono: 'JetBrains Mono, monospace'
  },
  fontSize: {
    xs: '0.75rem',
    sm: '0.875rem',
    base: '1rem',
    lg: '1.125rem',
    xl: '1.25rem',
    '2xl': '1.5rem'
  },
  fontWeight: {
    normal: 400,
    medium: 500,
    semibold: 600,
    bold: 700
  },
  lineHeight: {
    tight: 1.25,
    normal: 1.5,
    relaxed: 1.75
  }
};
```

### Phase 3: Component Architecture Planning

**Define component structure:**

```typescript
// ComponentName.types.ts
export interface ComponentNameProps {
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'sm' | 'md' | 'lg';
  isDisabled?: boolean;
  isLoading?: boolean;
  onClick?: () => void;
  children: React.ReactNode;
  className?: string;
  // ... other props
}

export interface ComponentNameState {
  isHovered: boolean;
  isFocused: boolean;
  // ... other state
}
```

**Component breakdown:**
- Identify sub-components
- Define prop interfaces
- Plan state management
- Consider composition patterns

### Phase 4: Implementation

**Follow this structure:**

```tsx
// Button.tsx - Example component
import React, { forwardRef } from 'react';
import { cn } from '@/lib/utils';
import { buttonVariants, type ButtonProps } from './Button.variants';

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      variant = 'primary',
      size = 'md',
      isDisabled = false,
      isLoading = false,
      children,
      className,
      ...props
    },
    ref
  ) => {
    return (
      <button
        ref={ref}
        disabled={isDisabled || isLoading}
        className={cn(buttonVariants({ variant, size }), className)}
        aria-disabled={isDisabled}
        aria-busy={isLoading}
        {...props}
      >
        {isLoading ? (
          <>
            <span className="sr-only">Loading...</span>
            <LoadingSpinner size={size} />
          </>
        ) : (
          children
        )}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

### Phase 5: Design System Integration

**Component documentation template:**

```markdown
# ComponentName

## Description
Brief description of what this component does and when to use it.

## Usage

\`\`\`tsx
import { ComponentName } from '@/components/ComponentName';

<ComponentName variant="primary" size="md">
  Click me
</ComponentName>
\`\`\`

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'primary' \| 'secondary' | 'primary' | Visual style variant |
| size | 'sm' \| 'md' \| 'lg' | 'md' | Component size |

## Variants

### Primary
Used for main call-to-action buttons.

### Secondary
Used for secondary actions.

## Accessibility
- Keyboard accessible (Tab, Enter, Space)
- Screen reader friendly with proper ARIA labels
- Focus visible with clear outline
- Disabled state properly announced

## Design Tokens Used
- Colors: `primary.500`, `primary.600`
- Spacing: `md`, `lg`
- Typography: `base`, `medium`

## Examples

[Include visual examples or Storybook links]
```

## Design System Standards

### Component Naming Conventions

```
PascalCase for components:     Button, CardHeader, NavigationMenu
camelCase for utilities:       cn, formatDate, validateEmail
kebab-case for files:          button.tsx, card-header.tsx
SCREAMING_SNAKE for constants: MAX_ITEMS, API_ENDPOINT
```

### File Structure

```
components/
├── Button/
│   ├── Button.tsx           # Main component
│   ├── Button.types.ts      # TypeScript interfaces
│   ├── Button.variants.ts   # Variant styles (CVA)
│   ├── Button.stories.tsx   # Storybook stories
│   ├── Button.test.tsx      # Unit tests
│   ├── Button.md            # Documentation
│   └── index.ts             # Exports
```

### Variant Management with CVA

```typescript
// Button.variants.ts
import { cva, type VariantProps } from 'class-variance-authority';

export const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        primary: 'bg-primary-500 text-white hover:bg-primary-600',
        secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300',
        outline: 'border-2 border-primary-500 text-primary-500 hover:bg-primary-50'
      },
      size: {
        sm: 'h-9 px-3 text-sm',
        md: 'h-10 px-4 text-base',
        lg: 'h-11 px-6 text-lg'
      }
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md'
    }
  }
);

export type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> &
  VariantProps<typeof buttonVariants> & {
    isLoading?: boolean;
  };
```

## Collaborative Design Process

### Designer-Developer Handoff

**Designer provides:**
1. Design files with proper component naming
2. Component specifications document
3. Design token values
4. Interaction states and animations
5. Responsive behavior documentation
6. Accessibility requirements

**Developer provides:**
1. Technical feasibility feedback
2. Implementation timeline estimates
3. Proposed component API (props)
4. Edge case considerations
5. Performance implications

### Design Review Checklist

Before implementation:
- [ ] All states are designed (default, hover, active, focus, disabled, error, loading)
- [ ] Responsive behavior is specified
- [ ] Design tokens are properly applied
- [ ] Accessibility annotations are present
- [ ] Animation/transition specs are clear
- [ ] Edge cases are addressed
- [ ] Component is added to design system library

### Feedback Loop

```mermaid
Designer creates → Developer reviews → Designer adjusts → Developer implements → Designer verifies → Document in system
```

## Responsive Design Patterns

### Breakpoint Strategy

```typescript
// breakpoints.tokens.ts
export const breakpoints = {
  sm: '640px',   // Mobile landscape
  md: '768px',   // Tablet
  lg: '1024px',  // Desktop
  xl: '1280px',  // Large desktop
  '2xl': '1536px' // Extra large
};
```

### Mobile-First Approach

```tsx
// Always start with mobile styles, then enhance
<div className="
  px-4           // mobile
  md:px-6        // tablet
  lg:px-8        // desktop
  flex flex-col  // mobile: stack
  lg:flex-row    // desktop: side-by-side
">
  {/* content */}
</div>
```

## Animation & Interaction

### Transition Tokens

```javascript
export const transitions = {
  fast: '150ms ease-in-out',
  base: '200ms ease-in-out',
  slow: '300ms ease-in-out',
  
  // Specific properties
  color: 'color 200ms ease-in-out',
  transform: 'transform 200ms cubic-bezier(0.4, 0, 0.2, 1)',
  all: 'all 200ms ease-in-out'
};
```

### Micro-interactions

```tsx
// Hover states
const HoverCard = () => (
  <div className="
    transition-all duration-200
    hover:scale-105
    hover:shadow-lg
    active:scale-95
  ">
    {/* content */}
  </div>
);

// Focus states (always visible for accessibility)
const FocusButton = () => (
  <button className="
    focus-visible:ring-2
    focus-visible:ring-primary-500
    focus-visible:ring-offset-2
  ">
    Click me
  </button>
);
```

## Quality Assurance

### Visual Regression Testing

```typescript
// Button.visual.test.ts
import { test, expect } from '@playwright/test';

test.describe('Button visual tests', () => {
  test('matches primary variant snapshot', async ({ page }) => {
    await page.goto('/components/button');
    await expect(page.locator('[data-testid="button-primary"]'))
      .toHaveScreenshot('button-primary.png');
  });
  
  test('matches hover state', async ({ page }) => {
    await page.goto('/components/button');
    const button = page.locator('[data-testid="button-primary"]');
    await button.hover();
    await expect(button).toHaveScreenshot('button-primary-hover.png');
  });
});
```

### Design-Code Comparison

Use tools like:
- **Chromatic**: Automated visual testing
- **Percy**: Visual regression platform
- **Storybook**: Component explorer and documentation

## Resources & Tools

### Design Tools
- **Figma**: Primary design tool with dev mode
- **Tokens Studio**: Figma plugin for design tokens
- **Anima**: Figma to code export

### Development Tools
- **CVA**: Class variance authority for variant management
- **Tailwind CSS**: Utility-first CSS framework
- **Radix UI**: Unstyled, accessible component primitives
- **Storybook**: Component development environment

### Documentation
- [Material Design System](https://m3.material.io/)
- [Atlassian Design System](https://atlassian.design/)
- [Shopify Polaris](https://polaris.shopify.com/)

## Validation Criteria

A component is ready for production when:
- [ ] Matches design specifications exactly
- [ ] Uses design tokens consistently
- [ ] Passes accessibility audit (WCAG 2.1 AA)
- [ ] Has responsive behavior implemented
- [ ] Includes all interaction states
- [ ] Has comprehensive tests (unit, visual, accessibility)
- [ ] Is documented with examples
- [ ] Receives designer approval
- [ ] Performs well (no layout shift, smooth animations)

---

**Last Updated**: 2026-02-18
**Version**: 1.0.0