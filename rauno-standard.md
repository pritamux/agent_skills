# Recursive Design Evaluation (Rauno Standard)

## Purpose

This skill enables AI agents to conduct thorough, iterative design critiques and quality assessments inspired by the meticulous design standards of Rauno Freiberg and other world-class design engineers. It provides a systematic framework for evaluating visual design, interaction patterns, and overall user experience quality.

## Philosophy

> "Great design is invisible. It gets out of the way and lets users accomplish their goals without friction or frustration."

### Core Tenets

1. **Details Matter**: Every pixel, transition, and interaction counts
2. **Consistency First**: Patterns should be predictable and familiar
3. **Performance is UX**: Slow interfaces are bad interfaces
4. **Accessibility is Non-Negotiable**: Everyone deserves great experiences
5. **Less is More**: Simplicity scales better than complexity

## Recursive Evaluation Framework

### Level 1: First Impression (5-second test)

**Ask:**
- What's the immediate visual hierarchy?
- Where does the eye naturally go first?
- Is the purpose of this interface clear?
- Does it feel modern and polished?

**Red Flags:**
- Cluttered or overwhelming layout
- Unclear primary action
- Inconsistent spacing or alignment
- Poor typography hierarchy

### Level 2: Visual Design Critique

#### Typography Assessment

```
✅ Good Typography:
- Clear hierarchy (3-4 distinct levels)
- Appropriate font sizes (min 16px for body)
- Comfortable line height (1.5-1.75 for body)
- Optimal line length (45-75 characters)
- Proper font weights (400 for body, 500-600 for emphasis, 700 for headings)

❌ Bad Typography:
- Too many font sizes (>5 distinct sizes)
- Body text below 14px
- Line height too tight (<1.3) or too loose (>2)
- Lines longer than 100 characters
- Inconsistent font weights
```

**Checklist:**
- [ ] Font family is legible and appropriate for context
- [ ] Heading hierarchy is clear (h1 > h2 > h3)
- [ ] Body text is comfortable to read
- [ ] Font weights create clear visual distinction
- [ ] Letter spacing is appropriate for size

#### Color & Contrast

```
✅ Good Color Usage:
- Purposeful color palette (1-2 primary, 2-3 secondary)
- Consistent semantic colors (success=green, error=red)
- Text contrast meets WCAG AA (4.5:1 for normal, 3:1 for large)
- Colors have meaning and aren't decorative only

❌ Bad Color Usage:
- Rainbow of colors without purpose
- Poor contrast (gray text on light background)
- Inconsistent semantic meanings
- Overly saturated or neon colors
```

**Evaluation Criteria:**
- [ ] All text meets WCAG contrast requirements
- [ ] Color is not the only indicator of meaning
- [ ] Palette feels cohesive and intentional
- [ ] Interactive elements have clear hover/active states
- [ ] Dark mode (if applicable) is equally well-considered

#### Spacing & Layout

```
✅ Good Spacing:
- Consistent spacing scale (8px base or 4px)
- Adequate whitespace around elements
- Clear visual grouping through proximity
- Comfortable padding on interactive elements (min 44x44px touch targets)

❌ Bad Spacing:
- Random spacing values (13px, 17px, 22px)
- Elements too cramped
- Inconsistent margins
- Touch targets too small
```

**8-Point Grid System:**
```css
/* Recommended spacing scale */
spacing: {
  0: 0,
  1: 0.25rem,  /* 4px */
  2: 0.5rem,   /* 8px */
  3: 0.75rem,  /* 12px */
  4: 1rem,     /* 16px */
  5: 1.25rem,  /* 20px */
  6: 1.5rem,   /* 24px */
  8: 2rem,     /* 32px */
  10: 2.5rem,  /* 40px */
  12: 3rem,    /* 48px */
  16: 4rem     /* 64px */
}
```

### Level 3: Interaction Design Review

#### Micro-interactions

**Elements to Evaluate:**

1. **Hover States**
   - [ ] Present on all interactive elements
   - [ ] Subtle but noticeable change
   - [ ] Smooth transition (150-200ms)
   - [ ] Cursor changes to pointer

2. **Active/Pressed States**
   - [ ] Visual feedback on click/tap
   - [ ] Slightly darker or scale down (0.95-0.98)
   - [ ] Immediate response (<100ms)

3. **Focus States**
   - [ ] Visible focus ring on keyboard nav
   - [ ] High contrast focus indicator
   - [ ] Not just relying on outline
   - [ ] Consistent across all components

4. **Loading States**
   - [ ] Clear indication that action is processing
   - [ ] Prevents duplicate submissions
   - [ ] Maintains layout (no shift)
   - [ ] Has timeout/error handling

5. **Disabled States**
   - [ ] Visually distinct (lower opacity or gray)
   - [ ] Cursor changes to not-allowed
   - [ ] Tooltip explains why disabled (optional)

#### Animation & Transitions

```
✅ Good Animations:
- Purposeful (guides attention or provides feedback)
- Fast (100-300ms for UI, 300-500ms for transitions)
- Smooth (ease-out or custom cubic-bezier)
- Respects prefers-reduced-motion
- Doesn't block user input

❌ Bad Animations:
- Gratuitous or distracting
- Too slow (>500ms for simple transitions)
- Linear easing (feels robotic)
- Ignores accessibility preferences
- Blocks interaction during animation
```

**Recommended Easing:**
```css
/* Sharp entrance */
ease-out: cubic-bezier(0.0, 0.0, 0.2, 1)

/* Gentle entrance */
ease-in-out: cubic-bezier(0.4, 0.0, 0.2, 1)

/* Emphasized entrance */
emphasized: cubic-bezier(0.4, 0.0, 0.0, 1)

/* Material Design standard */
standard: cubic-bezier(0.4, 0.0, 0.2, 1)
```

### Level 4: User Experience Patterns

#### Information Architecture

**Evaluate:**
- [ ] Content is organized logically
- [ ] Navigation is intuitive and findable
- [ ] User flows have clear entry and exit points
- [ ] Progressive disclosure is used appropriately
- [ ] No dead ends or orphaned pages

#### Error Handling

```tsx
// ✅ Good error handling
<form onSubmit={handleSubmit}>
  <Input
    label="Email"
    error={errors.email}
    hint="We'll never share your email"
  />
  {errors.submit && (
    <Alert variant="error" role="alert">
      {errors.submit}
    </Alert>
  )}
</form>

// ❌ Bad error handling
<form onSubmit={handleSubmit}>
  <input type="email" />
  {/* Generic alert() or console.log() */}
</form>
```

**Checklist:**
- [ ] Errors are shown inline near the problem
- [ ] Error messages are helpful, not technical
- [ ] Users can recover from errors easily
- [ ] Validation happens at appropriate times
- [ ] Success states are shown for important actions

#### Empty & Edge Cases

**Consider:**
- Empty states (no data, first-time use)
- Loading states (initial load, pagination)
- Error states (network failure, 404)
- Success states (confirmation, completion)
- Maximum content (very long text, many items)
- Minimum content (short text, one item)

### Level 5: Performance & Polish

#### Performance Metrics

```
Target Performance:
- First Contentful Paint: <1.0s
- Largest Contentful Paint: <2.5s
- Time to Interactive: <3.5s
- Cumulative Layout Shift: <0.1
- First Input Delay: <100ms
```

**Optimization Checklist:**
- [ ] Images are optimized (WebP, proper sizing)
- [ ] Fonts are subset and preloaded
- [ ] Critical CSS is inlined
- [ ] JavaScript is code-split
- [ ] Unnecessary animations are disabled on low-power mode

#### Polish Checklist

**Visual Polish:**
- [ ] No jagged edges or pixelation
- [ ] Consistent border radius throughout
- [ ] Shadows are subtle and consistent
- [ ] Icons are aligned and sized properly
- [ ] Images have proper aspect ratios

**Interactive Polish:**
- [ ] Smooth scrolling on all platforms
- [ ] No layout shift during interactions
- [ ] Gestures work on touch devices
- [ ] Keyboard shortcuts are discoverable
- [ ] Haptic feedback on mobile (where appropriate)

**Content Polish:**
- [ ] Copy is clear and concise
- [ ] Tone is consistent throughout
- [ ] No lorem ipsum in production
- [ ] Dates and numbers are formatted correctly
- [ ] Proper pluralization and i18n

## Quality Assessment Matrix

### Rating Scale (1-5)

**5 - Exceptional**
- Industry-leading quality
- Sets new standards
- Zero compromise on details
- Delightful to use

**4 - Excellent**
- Professional quality
- Few minor issues
- Meets all requirements
- Pleasant to use

**3 - Good**
- Functional and acceptable
- Some rough edges
- Meets most requirements
- Usable but not remarkable

**2 - Needs Improvement**
- Multiple issues present
- Missing key features
- Frustrating to use
- Requires significant work

**1 - Poor**
- Fundamentally broken
- Major usability issues
- Does not meet requirements
- Needs complete overhaul

### Evaluation Categories

```markdown
## Design Scorecard

### Visual Design
- Typography: __/5
- Color & Contrast: __/5
- Spacing & Layout: __/5
- Consistency: __/5

### Interaction Design
- Micro-interactions: __/5
- Animations: __/5
- Feedback: __/5
- Error Handling: __/5

### User Experience
- Information Architecture: __/5
- User Flows: __/5
- Edge Cases: __/5
- Empty States: __/5

### Technical Quality
- Performance: __/5
- Accessibility: __/5
- Responsiveness: __/5
- Browser Support: __/5

### Overall Score: __/80

Grade:
- 72-80: Exceptional (A)
- 64-71: Excellent (B)
- 56-63: Good (C)
- 48-55: Needs Improvement (D)
- <48: Poor (F)
```

## Iterative Improvement Process

### Phase 1: Identify Issues

1. **Conduct thorough evaluation** using the framework above
2. **Document all findings** with screenshots/recordings
3. **Prioritize issues** by severity and impact
4. **Create actionable tasks** with clear acceptance criteria

### Phase 2: Propose Solutions

For each issue:

```markdown
**Issue**: Button hover state is too subtle
**Impact**: Users unsure if element is interactive (Severity: Medium)
**Current**: opacity-90 on hover
**Proposed**: Increase brightness, add subtle scale transform
**Code**:
```css
.button:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  filter: brightness(1.05);
}
```
**Expected Outcome**: Clear visual feedback on hover
```

### Phase 3: Implement & Validate

1. **Implement fixes** following best practices
2. **Test thoroughly** across devices and browsers
3. **Validate with users** if possible
4. **Re-evaluate** using the same framework
5. **Iterate** until quality standards are met

### Phase 4: Document Learnings

```markdown
## Design Decision Log

### Decision: Button Hover Enhancement
**Date**: 2026-02-18
**Problem**: Low discoverability of interactive elements
**Solution**: Enhanced hover states with transform and shadow
**Result**: 40% increase in user engagement metrics
**Learnings**: Subtle doesn't always mean better; users need clear affordances
```

## Design Critique Framework

### Giving Feedback

**Structure:**

1. **Start with positives**: What works well?
2. **Identify issues**: What could be improved?
3. **Suggest solutions**: How to fix it?
4. **Explain reasoning**: Why does it matter?
5. **Prioritize**: What's most important?

**Example:**

```markdown
### Homepage Hero Section Critique

#### What Works Well ✅
- Strong typography hierarchy with clear heading
- Good use of whitespace around CTA
- Color palette is cohesive and on-brand

#### Areas for Improvement 🔄

**Issue 1: Low contrast on secondary text**
- Current: #64748b on #f8fafc (2.8:1 contrast)
- Recommendation: Use #475569 instead (4.6:1 contrast)
- Why: Meets WCAG AA standards, improves readability
- Priority: High (accessibility)

**Issue 2: CTA button lacks hover state**
- Current: No visual change on hover
- Recommendation: Add brightness increase and subtle lift
- Why: Improves discoverability and affordance
- Priority: Medium (usability)

**Issue 3: Heading text too large on mobile**
- Current: 64px on all screens
- Recommendation: Use fluid typography (clamp(32px, 5vw, 64px))
- Why: Prevents text from overwhelming small screens
- Priority: High (responsive design)
```

### Receiving Feedback

**Approach:**
- Listen without defensiveness
- Ask clarifying questions
- Consider the user's perspective
- Separate personal preference from usability
- Test assumptions with data when possible

## Resources & Inspiration

### Design Systems to Study
- [Vercel Design](https://vercel.com/design) - Clean, minimal, fast
- [Linear](https://linear.app) - Exceptional attention to detail
- [Stripe](https://stripe.com) - Professional, trustworthy
- [GitHub Primer](https://primer.style) - Comprehensive, accessible

### Tools for Evaluation
- **axe DevTools**: Accessibility testing
- **Lighthouse**: Performance and best practices
- **VisBug**: Visual debugging in browser
- **Polypane**: Multi-screen testing
- **Stark**: Color contrast and accessibility

### Further Reading
- [Refactoring UI](https://www.refactoringui.com/) by Adam Wathan & Steve Schoger
- [Laws of UX](https://lawsofux.com/) by Jon Yablonski
- [Inclusive Components](https://inclusive-components.design/) by Heydon Pickering

## Validation Criteria

A design is approved when:

- [ ] Overall score is 64+ (B grade or higher)
- [ ] No critical accessibility violations
- [ ] All interactive states are present and polished
- [ ] Performance metrics meet targets
- [ ] Responsive behavior is smooth across breakpoints
- [ ] Edge cases are handled gracefully
- [ ] Design is approved by stakeholders
- [ ] User testing validates usability (if applicable)

---

**Last Updated**: 2026-02-18
**Version**: 1.0.0
**Named in honor of**: Rauno Freiberg (@raunofreiberg) and the design engineering community