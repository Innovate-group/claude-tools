# Claude Code Tools

A collection of skills, commands, and agents for Claude Code. Pick the tools you need for your projects.

## Repository Structure

```
claude-tools/
├── shopify/                # Shopify theme development
│   ├── skills/             # Shopify and general-purpose skills
│   └── commands/           # Shopify commands (coming soon)
└── tiendanube/             # TiendaNube/Nuvemshop theme development
    ├── skills/             # TiendaNube-specific skills
    └── commands/           # TiendaNube-specific commands
```

## General Skills

| Skill | Description |
|-------|-------------|
| `js-style-guide` | JavaScript code style guidelines: no semicolons, arrow functions, ES modules, async/await |
| `skill-creator` | Guide for creating effective skills that extend Claude's capabilities |
| `mcp-builder` | Build high-quality MCP servers to integrate external APIs with LLMs |
| `vertical-slice-lambda-developer` | AWS Lambda functions using Vertical Slice Architecture with Node.js + TypeScript |

## Shopify Skills

| Skill | Description |
|-------|-------------|
| `shopify-liquid-developer` | Shopify Liquid theme development: sections, blocks, snippets, and templates |
| `shopify-liquid-shipping-estimator` | Shipping cost estimator sections for Shopify product pages (PDP) |

## TiendaNube Skills

| Skill | Description |
|-------|-------------|
| `tiendanube-bootstrap-frontend` | Frontend development for TiendaNube themes using Bootstrap 4 with Figma MCP integration |
| `tiendanube-javascript-api` | JavaScript API reference: LS object, cart operations, checkout SDK, events |
| `tiendanube-landing-pages` | Custom landing pages with handle-based routing and reorderable sections |
| `tiendanube-objects` | Complete reference for template objects, variables, and Twig filters |
| `tiendanube-theme-config` | Theme configuration: settings.txt, translations, defaults, sections |
| `twig-advanced` | Advanced Twig patterns: macros, embeds, includes, template inheritance |

## TiendaNube Commands

| Command | Description |
|---------|-------------|
| `tiendanube-component` | Interactive workflow for implementing TiendaNube theme components |
| `tiendanube-settings` | Add editable settings to existing theme components |
| `doc` | Document changes made to TiendaNube theme projects |

## Usage

1. Copy the skill folder you need to your project's `.claude/skills/` directory
2. The skill will be automatically available in Claude Code

```bash
# General skills (from shopify/)
cp -r shopify/skills/js-style-guide ~/.claude/skills/

# Shopify skills
cp -r shopify/skills/shopify-liquid-developer ~/.claude/skills/

# TiendaNube skills
cp -r tiendanube/skills/tiendanube-bootstrap-frontend ~/.claude/skills/

# TiendaNube commands
cp -r tiendanube/commands/tiendanube-component ~/.claude/commands/
```

## Skill Structure

Each skill follows this structure:

```
skill-name/
├── SKILL.md         # Required: Instructions and metadata
├── references/      # Optional: Technical documentation
└── scripts/         # Optional: Reusable tools
```
