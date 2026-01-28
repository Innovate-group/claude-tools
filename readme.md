# Claude Code Tools

A collection of skills, commands, and agents for Claude Code. Pick the tools you need for your projects.

## Repository Structure

```
claude-tools/
├── skills/          # Reusable skill definitions
└── commands/        # Custom commands (coming soon)
```

## Available Skills

| Skill | Description |
|-------|-------------|
| `js-style-guide` | JavaScript code style guidelines: no semicolons, arrow functions, ES modules, async/await |
| `shopify-liquid-developer` | Shopify Liquid theme development: sections, blocks, snippets, and templates |
| `shopify-liquid-shipping-estimator` | Shipping cost estimator sections for Shopify product pages (PDP) |
| `skill-creator` | Guide for creating effective skills that extend Claude's capabilities |
| `mcp-builder` | Build high-quality MCP servers to integrate external APIs with LLMs |
| `vertical-slice-lambda-developer` | AWS Lambda functions using Vertical Slice Architecture with Node.js + TypeScript |

## Usage

1. Copy the skill folder you need to your project's `.claude/skills/` directory
2. The skill will be automatically available in Claude Code

```bash
# Example: Add the shopify-liquid-developer skill
cp -r skills/shopify-liquid-developer ~/.claude/skills/
```

## Skill Structure

Each skill follows this structure:

```
skill-name/
├── SKILL.md         # Required: Instructions and metadata
├── references/      # Optional: Technical documentation
└── scripts/         # Optional: Reusable tools
```
