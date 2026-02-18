# Design Engineering Workflow

> Comprehensive skill file for AI agents handling design-to-development workflows, design systems, and collaborative design processes

## Overview

This skill guides AI agents through the complete design engineering lifecycle, from interpreting design files to implementing production-ready components that maintain design system consistency and facilitate effective design-developer collaboration.

**Target Role**: Design Engineer, Frontend Developer, Design System Maintainer
**Scope**: Design handoff, component implementation, design system integration
**Output**: Production-ready code that accurately reflects design specifications

## Core Principles

### 1. Design Fidelity
- Maintain pixel-perfect accuracy where specified
- Preserve design intent and visual hierarchy
- Respect designer decisions on spacing, typography, and color

### 2. System Thinking
- Identify reusable patterns across designs
- Abstract components appropriately
- Build with scalability and maintainability in mind

### 3. Collaboration
- Document design decisions and deviations
- Communicate technical constraints early
- Bridge the gap between design and engineering

### 4. Accessibility First
- Ensure designs meet WCAG 2.1 AA standards
- Advocate for inclusive design practices
- Implement accessible alternatives when designs lack them

## Workflow: Design-to-Development Process

### Phase 1: Design Analysis

#### Step 1.1: Initial Design Review
```markdown
**Actions:**
1. Import/access design files (Figma, Sketch, Adobe XD)
2. Review all artboards, pages, and design variants
3. Identify design system components vs. custom elements
4. Note responsive breakpoints and adaptive behaviors
5. Check for design annotations and developer notes

**Output:**
- Design inventory document
- Component hierarchy map
- List of questions for designers
```

#### Step 1.2: Design Token Extraction
```javascript
// Extract and document design tokens
const designTokens = {
  colors: {
    primary: '#1E40AF',
    secondary: '#10B981',
    // Extract from design file color styles
  },
  typography: {
    fontFamily: {
      sans: 'Inter, system-ui, sans-serif',
      mono: 'Fira Code, monospace'
    },
    fontSize: {
      xs: '0.75rem',   // 12px
      sm: '0.875rem',  // 14px
      base: '1rem',    // 16px
      // Map from design type scales
    }
  },
  spacing: {
    '1': '0.25rem',  // 4px
    '2': '0.5rem',   // 8px
    '4': '1rem',     // 16px
    // Extract from design spacing system
  },
  borderRadius: {
    sm: '0.125rem',
    md: '0.375rem',
    lg: '0.5rem',
    // Document corner radius values
  }
};
```

**Validation:**
- [ ] All color values extracted and named semantically
- [ ] Typography scale matches design specifications
- [ ] Spacing units are consistent and systematic
- [ ] Border radius, shadows, and effects documented

#### Step 1.3: Component Specification
```markdown
## Component: Button

### Variants
- Primary, Secondary, Outline, Ghost
- Sizes: sm, md, lg
- States: default, hover, active, disabled, loading

### Design Tokens Used
- Color: primary, secondary
- Typography: fontSize.sm, fontWeight.medium
- Spacing: padding.2, padding.4
- BorderRadius: md

### Interactions
- Hover: backgroundColor darken 10%
- Active: scale 0.98
- Focus: ring-2 ring-offset-2

### Accessibility
- Min touch target: 44x44px
- Color contrast: 4.5:1 minimum
- Focus indicator: visible outline
- ARIA: role="button", proper labeling

### Responsive Behavior
- Mobile: Full width on xs breakpoint
- Tablet/Desktop: Auto width with min-width
```

### Phase 2: Design System Integration

#### Step 2.1: Check Existing Design System
```bash
# Review existing design system structure
/design-system
  /tokens        # Design tokens
  /primitives    # Base components
  /components    # Composite components
  /patterns      # Layout patterns
  /utils         # Helper functions
```

**Questions to Answer:**
- Does a similar component already exist?
- Can this be built by composing existing components?
- Does this introduce new design tokens?
- Should this be a primitive or composite component?

#### Step 2.2: Token Implementation
```typescript
// tokens/colors.ts
export const colors = {
  // Semantic tokens (preferred)
  brand: {
    primary: 'var(--color-brand-primary)',
    secondary: 'var(--color-brand-secondary)',
  },
  feedback: {
    success: 'var(--color-success)',
    error: 'var(--color-error)',
    warning: 'var(--color-warning)',
    info: 'var(--color-info)',
  },
  // Base tokens
  blue: {
    50: '#EFF6FF',
    100: '#DBEAFE',
    // ... extracted from design
  }
} as const;

// Export CSS variables
export const cssVariables = `
  :root {
    --color-brand-primary: ${colors.blue[600]};
    --color-brand-secondary: ${colors.green[600]};
    /* ... */
  }
  
  [data-theme="dark"] {
    --color-brand-primary: ${colors.blue[400]};
    /* ... dark mode overrides */
  }
`;
```

#### Step 2.3: Component Architecture
```typescript
// components/Button/Button.types.ts
export interface ButtonProps {
  /** Visual style variant */
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost';
  
  /** Size of the button */
  size?: 'sm' | 'md' | 'lg';
  
  /** Disabled state */
  disabled?: boolean;
  
  /** Loading state with spinner */
  loading?: boolean;
  
  /** Full width on mobile */
  fullWidthMobile?: boolean;
  
  /** Icon to display before text */
  leftIcon?: React.ReactNode;
  
  /** Icon to display after text */
  rightIcon?: React.ReactNode;
  
  /** Click handler */
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
  
  /** Button content */
  children: React.ReactNode;
}

// components/Button/Button.styles.ts
import { cva } from 'class-variance-authority';

export const buttonStyles = cva(
  // Base styles
  'inline-flex items-center justify-center font-medium transition-all focus:outline-none focus:ring-2 focus:ring-offset-2',
  {
    variants: {
      variant: {
        primary: 'bg-primary text-white hover:bg-primary-dark active:bg-primary-darker',
        secondary: 'bg-secondary text-white hover:bg-secondary-dark',
        outline: 'border-2 border-primary text-primary hover:bg-primary-50',
        ghost: 'text-primary hover:bg-primary-50',
      },
      size: {
        sm: 'text-sm px-3 py-1.5 rounded-md',
        md: 'text-base px-4 py-2 rounded-lg',
        lg: 'text-lg px-6 py-3 rounded-lg',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);
```

### Phase 3: Implementation

#### Step 3.1: Component Development
```typescript
// components/Button/Button.tsx
import React from 'react';
import { buttonStyles } from './Button.styles';
import { ButtonProps } from './Button.types';
import { Spinner } from '../Spinner';

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      variant = 'primary',
      size = 'md',
      disabled = false,
      loading = false,
      fullWidthMobile = false,
      leftIcon,
      rightIcon,
      onClick,
      children,
      ...props
    },
    ref
  ) => {
    const isDisabled = disabled || loading;

    return (
      <button
        ref={ref}
        className={buttonStyles({
          variant,
          size,
          className: fullWidthMobile ? 'w-full sm:w-auto' : undefined,
        })}
        disabled={isDisabled}
        onClick={onClick}
        aria-busy={loading}
        {...props}
      >
        {loading && <Spinner size="sm" className="mr-2" />}
        {leftIcon && !loading && <span className="mr-2">{leftIcon}</span>}
        {children}
        {rightIcon && <span className="ml-2">{rightIcon}</span>}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

#### Step 3.2: Responsive Implementation
```typescript
// Use mobile-first approach matching design breakpoints
const ResponsiveCard = () => {
  return (
    <div className="
      // Mobile (default)
      p-4 space-y-3
      
      // Tablet (sm: 640px)
      sm:p-6 sm:space-y-4
      
      // Desktop (lg: 1024px)
      lg:p-8 lg:space-y-6
      
      // Wide (xl: 1280px)
      xl:max-w-7xl xl:mx-auto
    ">
      {/* Content */}
    </div>
  );
};
```

#### Step 3.3: Animation & Interactions
```typescript
// Implement micro-interactions from design
import { motion } from 'framer-motion';

const AnimatedButton = () => {
  return (
    <motion.button
      whileHover={{ scale: 1.02 }}
      whileTap={{ scale: 0.98 }}
      transition={{ type: 'spring', stiffness: 400, damping: 17 }}
    >
      Click me
    </motion.button>
  );
};

// CSS approach for simple transitions
const styles = {
  button: `
    transition-all duration-200 ease-in-out
    hover:shadow-lg hover:-translate-y-0.5
    active:translate-y-0
  `
};
```

### Phase 4: Design Review & Validation

#### Step 4.1: Visual QA Checklist
```markdown
## Visual Quality Assurance

### Layout & Spacing
- [ ] Margins match design specifications
- [ ] Padding is consistent with design system
- [ ] Grid alignment follows design layout
- [ ] Whitespace ratios preserved

### Typography
- [ ] Font families loaded and applied correctly
- [ ] Font sizes match across breakpoints
- [ ] Line heights maintain readability
- [ ] Letter spacing preserved
- [ ] Text alignment correct

### Colors
- [ ] Colors match design exactly (use color picker)
- [ ] Contrast ratios meet accessibility standards
- [ ] Hover/focus states implemented
- [ ] Dark mode variations (if applicable)

### Interactive Elements
- [ ] Hover effects smooth and intentional
- [ ] Click/tap feedback present
- [ ] Loading states implemented
- [ ] Disabled states styled appropriately
- [ ] Focus indicators visible and distinct

### Responsive Behavior
- [ ] Test on mobile (320px - 640px)
- [ ] Test on tablet (641px - 1024px)
- [ ] Test on desktop (1025px+)
- [ ] Images scale appropriately
- [ ] Text doesn't overflow containers
- [ ] Touch targets minimum 44x44px on mobile
```

#### Step 4.2: Design Comparison
```bash
# Tools for visual regression testing
npm install --save-dev @storybook/addon-designs

# Compare implementation against design
- Overlay design on implementation
- Check pixel alignment
- Verify spacing with browser dev tools
- Screenshot comparison (Percy, Chromatic)
```

#### Step 4.3: Design Handoff Documentation
```markdown
## Implementation Notes for Designers

### Component: ProductCard
**Status**: ✅ Implemented
**Design File**: designs/e-commerce/product-listing.fig
**Implementation**: components/ProductCard/

### Design Fidelity
- Matches design 98% (see notes below)
- Responsive breakpoints: 640px (sm), 1024px (lg)

### Technical Decisions
1. **Image Loading**: Added skeleton loader for better UX
2. **Hover State**: Implemented elevation change as designed
3. **Badge Position**: Adjusted by 2px for alignment with grid

### Deviations from Design
- **Price Typography**: Used fontWeight 600 instead of 700 for better legibility at small sizes
- **CTA Button**: Added 2px more padding for improved touch target on mobile

### Accessibility Enhancements
- Added alt text for product images
- Implemented keyboard navigation
- Color contrast ratio: 7.2:1 (exceeds WCAG AA)

### Browser Support
- ✅ Chrome/Edge 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ iOS Safari 14+
- ✅ Chrome Android 90+
```

## Best Practices

### 1. Design Token Management
```typescript
// ✅ Good: Semantic naming
const color = {
  text: {
    primary: 'var(--color-text-primary)',
    secondary: 'var(--color-text-secondary)',
  },
  background: {
    primary: 'var(--color-bg-primary)',
    secondary: 'var(--color-bg-secondary)',
  }
};

// ❌ Bad: Non-semantic naming
const color = {
  blue500: '#3B82F6',
  gray200: '#E5E7EB',
};
```

### 2. Component Composition
```typescript
// ✅ Good: Composable components
const Card = ({ children }) => <div className="card">{children}</div>;
const CardHeader = ({ children }) => <div className="card-header">{children}</div>;
const CardBody = ({ children }) => <div className="card-body">{children}</div>;

// Usage:
<Card>
  <CardHeader>Title</CardHeader>
  <CardBody>Content</CardBody>
</Card>

// ❌ Bad: Monolithic components
const Card = ({ title, content, footer, showBorder, variant, ... }) => {
  // 50+ lines of conditional rendering
};
```

### 3. Responsive Design Implementation
```css
/* ✅ Good: Mobile-first approach */
.component {
  padding: 1rem; /* Mobile */
}

@media (min-width: 768px) {
  .component {
    padding: 2rem; /* Tablet+ */
  }
}

/* ❌ Bad: Desktop-first */
.component {
  padding: 2rem;
}

@media (max-width: 767px) {
  .component {
    padding: 1rem;
  }
}
```

### 4. Design System Documentation
```typescript
// components/Button/Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  parameters: {
    design: {
      type: 'figma',
      url: 'https://www.figma.com/file/...',
    },
  },
  tags: ['autodocs'],
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    children: 'Button',
    variant: 'primary',
  },
};

export const AllVariants: Story = {
  render: () => (
    <div className="space-x-4">
      <Button variant="primary">Primary</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="ghost">Ghost</Button>
    </div>
  ),
};
```

## Common Pitfalls & Solutions

### Pitfall 1: Hardcoded Values
**Issue**: Using magic numbers instead of design tokens
```typescript
// ❌ Bad
<div style={{ marginTop: '24px', color: '#1E40AF' }}>

// ✅ Good
<div className="mt-6 text-primary">
```
**Solution**: Always reference design tokens

### Pitfall 2: Inconsistent Naming
**Issue**: Component names don't match design file names
**Solution**: Establish naming conventions early
```markdown
## Naming Convention
Design File → Component Name
Product Card → ProductCard
CTA Button → CTAButton (or CallToActionButton)
```

### Pitfall 3: Missing States
**Issue**: Forgetting to implement all interaction states
**Solution**: Use checklist for every interactive component
- [ ] Default
- [ ] Hover
- [ ] Active/Pressed
- [ ] Focus
- [ ] Disabled
- [ ] Loading
- [ ] Error (if applicable)

### Pitfall 4: Accessibility Afterthought
**Issue**: Adding accessibility features after implementation
**Solution**: Build accessibility in from the start
```typescript
// Include accessibility from first commit
const Button = ({ label, onClick }) => (
  <button
    onClick={onClick}
    aria-label={label}
    role="button"
    tabIndex={0}
  >
    {label}
  </button>
);
```

## Collaboration Guidelines

### Designer-Developer Communication

#### Weekly Sync Meeting Agenda
```markdown
1. Design Review (15 min)
   - New designs ready for dev
   - Design updates to existing components

2. Implementation Feedback (15 min)
   - Technical constraints discovered
   - Proposed design system additions
   - Performance considerations

3. Design System Updates (15 min)
   - New tokens added
   - Component deprecations
   - Breaking changes

4. Upcoming Work (15 min)
   - Next sprint designs
   - Design exploration needs
```

#### Handoff Checklist
```markdown
## Design Handoff Complete When:
- [ ] All artboards organized and named clearly
- [ ] Design tokens documented in spec
- [ ] All states and variants included
- [ ] Responsive behavior annotated
- [ ] Interactions/animations specified
- [ ] Copy finalized (no Lorem Ipsum)
- [ ] Assets exported and optimized
- [ ] Accessibility notes included
- [ ] Design file shared with developer access
- [ ] Synchronous walkthrough scheduled
```

### Feedback Loop
```markdown
## When Implementation Deviates from Design:

1. **Document the deviation**
   - What changed
   - Why it changed
   - Visual comparison

2. **Communicate proactively**
   - Tag designer in PR
   - Share preview link
   - Explain technical reasoning

3. **Seek approval**
   - Before merging
   - Document approval in PR

4. **Update design files**
   - Designer updates source of truth
   - Or: Create ticket for design update
```

## Quality Checklist

Before marking design implementation complete:

### Code Quality
- [ ] Components follow established patterns
- [ ] Design tokens used consistently
- [ ] No hardcoded values
- [ ] TypeScript types fully defined
- [ ] Props documented with JSDoc comments

### Visual Fidelity
- [ ] Pixel-perfect on target breakpoints
- [ ] Colors match exactly
- [ ] Typography specs implemented
- [ ] Spacing matches design system
- [ ] Animations/transitions smooth

### Functionality
- [ ] All interactive states work
- [ ] Responsive behavior correct
- [ ] Loading states implemented
- [ ] Error states handled

### Accessibility
- [ ] WCAG 2.1 AA compliant
- [ ] Keyboard navigation works
- [ ] Screen reader tested
- [ ] Focus indicators visible
- [ ] Color contrast verified

### Documentation
- [ ] Storybook stories created
- [ ] Props table complete
- [ ] Usage examples provided
- [ ] Design file linked
- [ ] Implementation notes added

### Testing
- [ ] Unit tests written
- [ ] Visual regression tests pass
- [ ] Cross-browser tested
- [ ] Mobile device tested

## Resources & References

### Tools
- **Figma**: Design collaboration and handoff
- **Storybook**: Component documentation
- **Chromatic**: Visual regression testing
- **Axe DevTools**: Accessibility testing
- **Design Tokens**: Style Dictionary, Theo

### Design Systems Examples
- [Material Design](https://material.io/design)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/)
- [Atlassian Design System](https://atlassian.design/)
- [Shopify Polaris](https://polaris.shopify.com/)

### Reading
- *Design Systems* by Alla Kholmatova
- *Atomic Design* by Brad Frost
- *Refactoring UI* by Adam Wathan & Steve Schoger

---

**Skill Version**: 1.0.0  
**Last Updated**: February 2026  
**Maintained By**: Agent Skills Repository