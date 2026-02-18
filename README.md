# Agent Skills Repository

> A curated collection of structured skill instructions and workflow guidelines for AI agents like Claude Code and Conductor

## 🎯 Purpose

This repository contains professionally crafted skill files that serve as actionable instruction sets for AI development agents. Each markdown file provides comprehensive guidelines, best practices, and systematic workflows that AI systems can reference and follow when performing specific software development tasks.

## 🤖 Compatible AI Systems

These skill files are designed to work with:
- **Claude Code** - Anthropic's AI coding assistant
- **Conductor** - AI workflow orchestration systems
- **GitHub Copilot Workspace** - AI-powered development environments
- **Custom AI Agents** - Any LLM-based development assistant

## 📚 How to Use These Skills

### For AI Agents

AI systems can reference these files to:
1. **Understand workflow expectations** - Follow established patterns and methodologies
2. **Apply best practices** - Implement industry-standard approaches automatically
3. **Maintain consistency** - Ensure uniform code quality and documentation
4. **Execute systematic processes** - Follow step-by-step procedures for complex tasks

### Integration Methods

#### Method 1: Direct Reference
```markdown
Agent, follow the guidelines in `design-claude.md` to complete the design handoff.
```

#### Method 2: Context Injection
```python
# Load skill file as context
with open('component-builder.md', 'r') as f:
    skill_context = f.read()
    
# Pass to AI agent
agent.execute(task="Build a Button component", context=skill_context)
```

#### Method 3: System Prompt Integration
```
System: You are an AI development assistant. When building components, 
refer to the guidelines in component-builder.md for architecture, 
accessibility, and documentation standards.
```

#### Method 4: Workflow Automation
```yaml
# GitHub Actions / CI/CD
steps:
  - name: AI Code Review
    run: |
      curl https://raw.githubusercontent.com/pritamux/agent_skills/main/code-review.md -o skill.md
      ai-agent review --skill-file skill.md --pr ${{ github.event.pull_request.number }}
```

## 📖 Available Skills

### Design & Development
- **[design-claude.md](./design-claude.md)** - Design engineering workflow with handoff processes, design systems integration, and collaborative practices
- **[component-builder.md](./component-builder.md)** - Comprehensive UI component development including architecture, accessibility, and testing

### Coming Soon
- **code-review.md** - Thorough code review methodology
- **documentation-generation.md** - Technical documentation standards
- **debugging-process.md** - Systematic debugging approaches
- **api-design.md** - REST API design best practices
- **testing-strategy.md** - Comprehensive testing guidelines
- **refactoring-guidelines.md** - Safe refactoring techniques
- **performance-optimization.md** - Performance analysis methods
- **security-audit.md** - Security review checklists

## 🎓 Skill File Structure

Each skill file follows a consistent structure:

```markdown
# Skill Title

## Overview
Clear description of the skill's purpose and scope

## Core Principles
Fundamental concepts and philosophies

## Workflow / Process
Step-by-step procedures to follow

## Best Practices
Industry-standard approaches and techniques

## Common Pitfalls
Mistakes to avoid and how to prevent them

## Quality Checklist
Verification steps to ensure success

## Resources & References
Additional learning materials
```

## 🚀 Quick Start Examples

### Example 1: Component Development
```bash
# Human instruction to AI agent:
"Create a Card component following the guidelines in component-builder.md.
Include accessibility features and comprehensive tests."
```

### Example 2: Design System Integration
```bash
# Human instruction to AI agent:
"Review the Figma design using design-claude.md workflow.
Generate component specifications with design tokens."
```

### Example 3: Custom Workflow
```bash
# Combine multiple skills:
"Using design-claude.md for design analysis and component-builder.md 
for implementation, create a responsive navigation component."
```

## 🛠️ Creating Custom Skills

To create your own skill files:

1. **Define the scope** - What specific task or workflow does this cover?
2. **Structure systematically** - Use clear sections with actionable steps
3. **Include examples** - Provide concrete code samples and scenarios
4. **Add validation criteria** - Define what "done" looks like
5. **Keep it actionable** - Focus on what to do, not just theory
6. **Test with AI** - Verify that AI agents can follow the instructions

### Skill Template
```markdown
# [Skill Name]

## Overview
[2-3 sentences describing the skill]

## Prerequisites
- Required knowledge
- Tools needed
- Context required

## Process
### Step 1: [Action]
- Specific instruction
- Expected outcome

### Step 2: [Action]
- Specific instruction
- Expected outcome

## Validation
- [ ] Checklist item 1
- [ ] Checklist item 2

## Examples
```code example```

## Common Issues
**Issue**: Description
**Solution**: How to resolve
```

## 💡 Best Practices for AI Agent Usage

1. **Be Specific** - Reference exact sections when giving instructions
2. **Combine Skills** - Use multiple skill files for complex tasks
3. **Provide Context** - Share relevant project information alongside skills
4. **Iterate** - Refine skills based on agent performance
5. **Version Control** - Track changes to skill files as requirements evolve

## 🤝 Contributing

Contributions are welcome! To add new skills or improve existing ones:

1. Fork this repository
2. Create a feature branch (`git checkout -b skill/new-skill-name`)
3. Follow the skill file structure outlined above
4. Test with an AI agent to ensure clarity
5. Submit a pull request with:
   - Clear description of the skill
   - Use cases and examples
   - Testing results with AI agents

## 📝 License

MIT License - Feel free to use these skills in your projects, customize them for your team, or integrate them into your AI agent workflows.

## 🔗 Related Resources

- [Anthropic Claude Documentation](https://docs.anthropic.com/)
- [GitHub Copilot Best Practices](https://github.blog/)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [AI-Assisted Development Patterns](https://martinfowler.com/)

## 📬 Feedback

Have suggestions for new skills or improvements? Open an issue or reach out!

---

**Made with 🤖 for AI Agents by Humans**

*Last Updated: February 2026*