# Agent Skills

> A curated collection of structured skill instructions and workflow guidelines for AI agents like Claude Code, Conductor, and other AI-powered development tools.

## Overview

This repository contains professionally crafted skill files that provide AI agents with clear, actionable instructions for various software development tasks. Each skill file is designed to be referenced by AI systems to enhance their capabilities and ensure consistent, high-quality outputs.

## What are Agent Skills?

Agent skills are structured markdown files that contain:
- **Clear objectives**: What the AI should accomplish
- **Step-by-step workflows**: How to approach tasks systematically
- **Best practices**: Industry standards and proven methodologies
- **Examples**: Concrete illustrations of expected outputs
- **Quality criteria**: How to evaluate and validate results

## Available Skills

### 🎨 Design & Development

#### [design-claude.md](./design-claude.md)
Comprehensive design engineering workflow covering:
- Design systems integration
- Component creation and documentation
- Design-to-code workflows
- Collaborative design processes
- Design token management

#### [component-builder.md](./component-builder.md)
UI component development best practices including:
- React/Next.js patterns and conventions
- Component architecture and composition
- Responsive design principles
- Accessibility standards (WCAG 2.1)
- Testing strategies for components

#### [rauno-standard.md](./rauno-standard.md)
Recursive design evaluation methodology featuring:
- Design critique frameworks
- Iterative improvement processes
- Quality assessment standards
- Visual hierarchy analysis
- User experience optimization

## Usage Instructions

### For AI Agents (Claude Code, Conductor, etc.)

When working on a task, AI agents can reference these skill files to:

1. **Understand context**: Read the skill file relevant to the current task
2. **Follow workflows**: Apply the step-by-step processes outlined
3. **Apply standards**: Ensure outputs meet quality criteria specified
4. **Validate results**: Use checklists to verify completeness

### For Developers

You can prompt AI agents to use these skills:

```markdown
# Example Prompt

"Please review the design-claude.md skill file and help me create 
a new button component following the design system guidelines."
```

```markdown
# Another Example

"Using the component-builder.md standards, build a responsive 
card component with proper accessibility features."
```

### Integration Examples

#### Claude Code (Artifacts)

```
@agent-skills/design-claude.md

Create a design system color palette component with proper 
token structure and documentation.
```

#### Conductor

```yaml
skills:
  - agent_skills/component-builder.md
  - agent_skills/rauno-standard.md

task: "Build and critique a new navigation component"
```

#### Custom AI Workflows

```python
# Load skill context
with open('agent_skills/component-builder.md', 'r') as f:
    skill_context = f.read()

# Include in AI prompt
prompt = f"""
{skill_context}

Task: Create a modal component following the above guidelines.
"""
```

## File Structure

Each skill file follows a consistent structure:

```markdown
# Skill Name

## Purpose
Clear statement of what this skill helps accomplish

## Principles
Core philosophies and approaches

## Workflow
Step-by-step process to follow

## Standards
Specific requirements and criteria

## Examples
Concrete illustrations

## Validation
How to verify quality and completeness

## Resources
References and further reading
```

## Contributing

To add new skills or improve existing ones:

1. **Maintain clarity**: Use clear, concise language
2. **Be actionable**: Provide specific steps, not vague guidance
3. **Include examples**: Show, don't just tell
4. **Add validation criteria**: Define what "done" looks like
5. **Keep it focused**: One skill per file

## Best Practices for Skill Files

### ✅ Do:
- Use clear, hierarchical headings
- Provide concrete examples with code blocks
- Include checklists for validation
- Reference industry standards
- Keep content up-to-date

### ❌ Don't:
- Use vague or ambiguous language
- Overcomplicate with unnecessary details
- Assume prior knowledge without context
- Mix multiple unrelated skills in one file

## Skill Development Roadmap

Planned future skills:
- **testing-strategy.md**: Comprehensive testing approaches
- **api-design.md**: REST API design best practices
- **performance-optimization.md**: Performance analysis techniques
- **security-audit.md**: Security review workflows
- **code-review.md**: Systematic code review process
- **documentation-generation.md**: Technical documentation standards

## Use Cases

### 1. Onboarding AI Agents
Provide context-rich instructions for AI systems joining a project

### 2. Standardizing Workflows
Ensure consistent approaches across different AI-assisted tasks

### 3. Quality Assurance
Define clear criteria for evaluating AI-generated outputs

### 4. Learning Resource
Help developers understand best practices through AI-readable formats

### 5. Documentation
Serve as living documentation for team standards and processes

## Version Control

Skill files are versioned through Git. Each significant update should:
- Include clear commit messages
- Update the relevant sections
- Maintain backward compatibility when possible
- Document breaking changes in commit messages

## License

Feel free to use, modify, and distribute these skill files for your projects.

## Feedback

Have suggestions for new skills or improvements? Open an issue or submit a pull request!

---

**Made for AI agents, by humans who work with them** 🤖✨