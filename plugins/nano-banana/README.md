# Nano Banana Plugin

Interactive JSON Prompt Translator for Nano Banana Pro (Gemini 3 Pro Image).

## Overview

This plugin provides the `/nano-banana` slash command that transforms natural language descriptions into structured JSON schemas for precision image generation with Nano Banana Pro (Gemini 3 Pro Image).

**Philosophy**: Stop rolling dice with vague prompts. Start turning dials with structured specifications where every element—lighting, camera angle, composition, props—lives on its own controllable parameter. Change one thing without affecting everything else.

## What is Nano Banana Pro?

[Nano Banana Pro](https://blog.google/technology/google-labs/gemini-image-generation-updates/) is Google's advanced image generation model (Gemini 3 Pro Image) that excels at rendering structured JSON specifications rather than vague text prompts. It's available through:
- Google Gemini app (select "Create images" → "Thinking" model)
- Google AI Studio

## Installation

First, add the marketplace to Claude Code:

```bash
/plugin marketplace add https://github.com/jawhnycooke/claude-code-nano-banana
```

Then install the plugin:

```bash
/plugin install nano-banana
```

## Command Reference

### Basic Usage

```bash
/nano-banana [description of image, UI, or diagram you want to create]
```

**Examples:**

```bash
# Marketing image
/nano-banana vinyl record product shot, moody lighting, vintage feel

# UI mockup
/nano-banana dashboard for analytics app with dark theme

# Diagram
/nano-banana flowchart showing user login process

# Data visualization
/nano-banana bar chart comparing quarterly revenue by region

# Social graphic
/nano-banana Instagram post announcing new product launch
```

### Without Initial Description

If you invoke the command without arguments, it will prompt you:

```bash
/nano-banana
```

The translator will ask: "What would you like to create? (Product photo, UI mockup, or diagram/infographic?)"

## How It Works

The command follows a collaborative workflow:

### 1. Classification
Identifies which schema type best fits your description:
- **Marketing Image** - Product shots, hero images, brand photography
- **UI/UX Mockup** - App screens, dashboards, websites, interfaces
- **Diagram** - Flowcharts, architecture diagrams, process maps
- **Data Visualization** - Charts, graphs, statistics with exact values
- **Social Graphic** - Platform-specific social media content

### 2. Interactive Discovery
Uses structured questions to gather requirements:
- **AskUserQuestion tool** for decisions with 2-4 clear options (camera angle, lighting style, platform, etc.)
- **Conversational follow-ups** for open-ended details (product name, specific data values, etc.)

### 3. JSON Generation
Builds a complete, valid JSON specification with:
- All required fields populated
- Reasonable defaults for unspecified values
- Unique IDs and valid references
- No placeholder values or incomplete data

### 4. Next Steps Guidance
Provides instructions for using the JSON with Nano Banana Pro

## Schema Types

### Marketing Image (`marketing_image`)

Product photography, hero images, brand campaigns.

**Key Elements:**
- Subject (product type, dimensions, finish)
- Props (foreground, midground, background elements)
- Environment (surface, background, atmosphere)
- Camera (angle, framing, focal length, depth of field)
- Lighting (key/fill/rim lights, direction, intensity)
- Brand constraints (colors, logo placement, forbidden changes)

**Common Values:**
- Camera angles: `front`, `three_quarter_front`, `side`, `top_down`, `overhead`
- Framing: `close_up`, `medium_close`, `medium`, `wide`
- Lighting direction: `left`, `right`, `front`, `back`, `top`

### UI/UX Mockup (`ui_builder`)

Application interfaces, dashboards, websites.

**Key Elements:**
- Platform (web, mobile, tablet, desktop)
- Fidelity level (wireframe, low-fi, mid-fi, hi-fi)
- Design tokens (colors, typography, spacing, border radius)
- Screens and layouts
- Components (buttons, forms, charts, tables, navigation)
- Data bindings

**Platform Options:**
- `web` - Browser-based (1280-1920px)
- `mobile` - iOS/Android phone (375-428px)
- `tablet` - iPad/Android tablet (768-1024px)
- `desktop` - Native desktop application

### Diagram (`diagram_spec`)

Flowcharts, architecture diagrams, process maps, system visualizations.

**Key Elements:**
- Canvas (dimensions, flow direction)
- Semantics (diagram type, relationship type, swimlanes)
- Nodes (with roles: start, end, process, decision, database, actor)
- Edges (connections between nodes with labels)
- Groups (logical groupings or containers)
- Legend

**Diagram Types:**
- `flowchart` - Process flows with decisions
- `architecture` - System components and connections
- `sequence` - Time-ordered interactions
- `swimlane` - Cross-functional processes
- `mindmap` - Central concept with branches

### Data Visualization (`data_viz`)

Charts and graphs where numerical accuracy is critical.

**Key Elements:**
- Chart type (bar, line, pie, scatter, etc.)
- Data series with exact values
- Axes configuration (labels, ranges, formatting)
- Annotations (data labels, legend, callouts, gridlines)
- Style settings
- Data lock constraints (enforce exact values)

**Chart Types:**
- `bar`, `horizontal_bar` - Compare categories
- `line`, `area` - Show trends over time
- `pie`, `donut` - Parts of a whole
- `scatter`, `bubble` - Correlations
- `treemap`, `heatmap` - Hierarchical or dense data

### Social Graphic (`social_graphic`)

Platform-specific social media content.

**Key Elements:**
- Platform/dimensions (Instagram, Twitter, LinkedIn, YouTube)
- Background (solid, gradient, image, pattern)
- Text layers (headline, subhead, CTA with positioning)
- Visual elements (product images, shapes, icons)
- Brand elements (logo, colors, fonts)

**Platform Dimensions:**
- `instagram_post` - 1080×1080px (square)
- `instagram_story` - 1080×1920px (vertical)
- `twitter_card` - 1200×675px (landscape)
- `linkedin_post` - 1200×627px (landscape)
- `youtube_thumbnail` - 1280×720px (landscape)

## Example Workflows

### Marketing Image Example

**Input:**
```bash
/nano-banana lime seltzer can with ice and lime slices, dark background, premium feel
```

**Interaction:**
1. Translator asks about camera angle (structured choice)
2. Asks about lighting style (structured choice)
3. Conversational follow-up: "What's the brand name?"
4. Conversational follow-up: "Any specific brand colors?"
5. Generates complete JSON with all specifications

**Output:**
Complete `marketing_image` JSON with subject, props, environment, camera, lighting, and brand sections ready to paste into Nano Banana Pro.

### UI Mockup Example

**Input:**
```bash
/nano-banana analytics dashboard for marketing team
```

**Interaction:**
1. Asks about platform (web/mobile/tablet/desktop)
2. Asks about theme (light/dark/brand colors)
3. Asks about fidelity level
4. Conversational: "How many screens do you need?"
5. Conversational: "What components should appear?"
6. Generates complete JSON

**Output:**
Complete `ui_builder` JSON with screens, components, layout, and design tokens.

### Diagram Example

**Input:**
```bash
/nano-banana user signup flow from landing page to activation
```

**Interaction:**
1. Asks diagram type (flowchart/architecture/swimlane)
2. Asks flow direction (left-to-right/top-to-bottom)
3. Conversational: "What are the main steps?"
4. Conversational: "Any decision points?"
5. Generates complete JSON

**Output:**
Complete `diagram_spec` JSON with nodes, edges, positioning, and styling.

## Command Behavior

### Proactive Structured Questions

The command **automatically** uses the AskUserQuestion tool for decisions with clear options:
- Schema type selection
- Camera angles and lighting
- Platform and theme selection
- Diagram types and flow direction
- Chart types and annotation options

You don't need to ask "help me decide" - the command offers structured choices by default.

### Conversational Follow-ups

For open-ended details, the command uses conversation:
- Product names and descriptions
- Specific data values
- Color hex codes
- Custom text content
- Unique requirements

### Intelligent Defaults

The command makes reasonable assumptions for unspecified fields rather than asking about every single detail. You can always refine the JSON after generation.

## Output Format

The command outputs **clean, raw JSON** (not wrapped in markdown code blocks) for easy copying. After the JSON, it provides next steps:

```
## Next Steps

1. **Review** the JSON above - make sure it captures your intent
2. **Copy** the entire JSON (select all, copy)
3. **Open Nano Banana Pro**:
   - In Gemini app: Select "Create images" → Choose "Thinking" model
   - Or use Google AI Studio
4. **Paste and generate** with instruction: "Render this specification as a high-fidelity image"
5. **Iterate** - To make changes, modify specific fields in the JSON and re-render

💡 **Tip**: The power of JSON specs is scoped editing. Want different lighting?
Just change the lighting fields. Everything else stays locked.
```

## Core Concept: Handles

The power of JSON prompting comes from the **handle concept** - every important element gets a stable identifier that you can manipulate independently:

### Scoped Edits
Change only the background, or only the lighting, without affecting other elements. Want warmer lighting? Modify just the `lighting.color_temperature` field.

### Camera Moves
Same scene, different perspective. Change `camera.angle` from `front` to `three_quarter_front` while keeping everything else locked.

### Themed Variants
Same structure, different visual styling. Swap color tokens in a UI mockup to create light/dark theme variants instantly.

### A/B Testing
Compare two versions that differ by exactly one variable. Perfect for testing which camera angle or lighting setup works better.

## Iteration Workflow

The JSON specifications enable precision iteration:

1. Generate initial JSON with `/nano-banana`
2. Render in Nano Banana Pro
3. Identify what to change (e.g., "lighting too dark")
4. Modify only the relevant JSON fields (e.g., `lighting.key_light_intensity`)
5. Re-render with updated JSON
6. Everything else (composition, props, colors) stays identical

This is the "dials, not dice" philosophy - surgical edits without re-rolling everything.

### Common Iteration Patterns

**Marketing Images:**
- **Lighting only**: Modify `lighting` section, keep subject and camera locked
- **Camera angle only**: Change `camera.angle` and `camera.focal_length_mm`
- **Props adjustment**: Add/remove items in `props.foreground` or `props.midground`

**UI Mockups:**
- **Theme swap**: Modify `tokens.color` while keeping layout unchanged
- **Add component**: Insert new object in `components` array with valid IDs
- **Layout adjustment**: Modify `containers` in screen layout

**Diagrams:**
- **Add node**: Insert new node in `nodes` array with unique ID
- **Reroute flow**: Modify `edges` to change connections
- **Visual refresh**: Update node `style` fields for new color scheme

**Data Visualizations:**
- **Update values**: Modify `data_series[].data_points` values
- **Chart type swap**: Change `chart_type` from `bar` to `line`
- **Add annotation**: Insert callout in `annotations` array

**Social Graphics:**
- **Platform resize**: Change `platform` value, dimensions auto-adjust
- **Color scheme**: Modify `background.gradient.colors`
- **Text update**: Change `text_layers[].content` for headlines/CTAs

## Tutorial

See the comprehensive tutorial: [Dials, Not Dice: A Precision Prompting Guide for Nano Banana Pro and Claude Code](https://jawhnycooke.ai/blog/precision-prompting-nano-banana-pro-with-claude-code)

The `examples/vinyl-record-tutorial/` folder in the repository contains the JSON files from the tutorial.

## Plugin Components

This plugin includes both a **slash command** (user-initiated) and a **skill** (Claude-initiated):

### Slash Command: `/nano-banana`

User-initiated command for interactive JSON generation. You explicitly invoke it when you want to create a structured specification.

**When to use:**
- You know you want a JSON spec for Nano Banana Pro
- You want guided interaction through structured questions
- You prefer explicit control over the translation process

### Skill: `json-prompting-for-nano-banana`

Claude can automatically invoke this skill during conversations when it detects you need precision image generation capabilities.

**When Claude invokes it:**
- You mention creating marketing images with exact product specifications
- You describe UI mockups that need precise layouts and components
- You need diagrams where process flows must be clear
- You want data visualizations with numerical accuracy
- You request social media graphics with platform-specific dimensions
- You emphasize reproducibility or brand consistency across image generations

**When Claude won't invoke it:**
- You want creative exploration with surprise outcomes
- You prefer vibes-based prompting
- A simple natural-language prompt would suffice
- You haven't defined what you actually want yet

The skill provides the same JSON translation capabilities as the slash command, but can be triggered naturally during conversation without explicit invocation.

## Files

```
plugins/nano-banana/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── commands/
│   └── nano-banana.md       # Slash command implementation
├── skills/
│   └── SKILL.md             # JSON prompting skill (json-prompting-for-nano-banana)
└── README.md                # This file
```

## Implementation Details

### Slash Command (`commands/nano-banana.md`)
- YAML frontmatter (name, description, version, argument hint)
- Comprehensive prompt engineering
- Structured question patterns using AskUserQuestion
- Complete JSON examples for each schema type
- Interactive workflow with user guidance

### Skill (`skills/SKILL.md`)
Skill name: `json-prompting-for-nano-banana`

- YAML frontmatter with invocation criteria
- Five schema types: `marketing_image`, `ui_builder`, `diagram_spec`, `data_viz`, `social_graphic`
- Handle-based editing concept for scoped changes
- Complete schema examples and iteration patterns
- Programmatic workflow for autonomous invocation
- Version history tracking

## Version

1.0.0

## Author

**Jawhny Cooke**
Contact: plugins@jawhnycooke.ai

## License

MIT

## Contributing

Issues and suggestions welcome at: https://github.com/jawhnycooke/claude-code-nano-banana/issues
