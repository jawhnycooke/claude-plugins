# Video/Animation Reference Guide

Comprehensive guide to animating anime images using Midjourney's V1 video model.

> **Important**: Video generation is available on the **Midjourney web app only** (midjourney.com). Discord bot does not support video generation.

> **Model Note**: Animation works with images generated from both Niji 6 and V7 native. Examples show `--niji 6` but images created with `--v 7` can also be animated using the same workflow.

## Overview

Midjourney's V1 video model transforms still images into animated clips. The workflow is **image-to-video**: generate your anime image first, then animate it.

### Video Specifications

| Spec | Value |
|------|-------|
| Duration | 5 seconds (extendable to 21 seconds) |
| Resolution | 480p (Standard) / 720p (Pro/Mega plans) |
| Frame Rate | 24 FPS |
| Audio | None |
| Format | MP4, GIF |

## Video Parameters

| Parameter | Description | Usage |
|-----------|-------------|-------|
| `--motion low` | Subtle, gentle movement | Default - cinemagraphs, peaceful scenes, slow motion |
| `--motion high` | Dynamic, energetic movement | Action scenes, big camera motions, combat |
| `--raw` | Reduce AI interpretation | Precise motion control, literal prompt adherence |
| `--loop` | Seamless looping | Start/end frames match automatically |
| `--end` | End frame control | Specify ending state of animation |
| `--bs #` | Batch size | Generate multiple videos at once |

> **Note**: Videos are generated via the web app interface, not through a `--video` parameter.

### Basic Syntax

```
[Image URL] [motion description] --motion low
[Image URL] [motion description] --motion high
```

### With Raw Mode (Precise Control)

```
[Image URL] [motion description] --motion low --raw
```

### With Loop

```
[Image URL] [motion description] --motion low --loop
```

## Motion Styles

### Action Style
High-energy movement with dynamic camera work.

```
[Image URL] dynamic movement, action lines, speed effects --motion high
```

**Best for**: Shonen action scenes, combat, chase sequences

### Slice-of-Life Style
Gentle, natural movement with subtle animation.

```
[Image URL] gentle breeze, hair flowing softly, subtle movement, peaceful --motion low --raw
```

**Best for**: Everyday scenes, character moments, atmospheric shots

### Dramatic Style
Cinematic movement with emotional impact.

```
[Image URL] slow camera push, emotional moment, cinematic --motion low
```

**Best for**: Emotional reveals, character close-ups, pivotal moments

### Combat Style
Fast, impactful action with dynamic effects.

```
[Image URL] fast motion, impact frames, explosive movement --motion high
```

**Best for**: Fight scenes, special attacks, transformation sequences

## Camera Motion Guide

### Pan (Horizontal/Vertical Movement)

| Direction | Keywords | Example |
|-----------|----------|---------|
| Pan Left | "camera pans left", "tracking left" | `[URL] camera pans left, following character --motion low` |
| Pan Right | "camera pans right", "tracking right" | `[URL] camera pans right, revealing scene --motion low` |
| Tilt Up | "camera tilts up", "vertical pan up" | `[URL] camera tilts up, dramatic reveal --motion low` |
| Tilt Down | "camera tilts down", "looking down" | `[URL] camera tilts down, showing ground --motion low` |

### Zoom (In/Out Movement)

| Type | Keywords | Example |
|------|----------|---------|
| Zoom In | "camera pushes in", "zoom to face", "dramatic push" | `[URL] slow zoom to face, emotional moment --motion low` |
| Zoom Out | "camera pulls back", "wide reveal", "zoom out" | `[URL] camera pulls back, revealing environment --motion low` |

### Orbit (Circular Movement)

| Type | Keywords | Example |
|------|----------|---------|
| Full Orbit | "camera orbits around", "360 rotation" | `[URL] camera orbits around character, showcase --motion low` |
| Partial Orbit | "camera arcs around", "semi-circle" | `[URL] camera arcs around, dynamic angle --motion high` |

### Static (Character Animation Only)

| Type | Keywords | Example |
|------|----------|---------|
| Breathing | "subtle breathing", "chest movement" | `[URL] subtle breathing motion, peaceful --motion low --raw` |
| Hair/Cloth | "hair swaying", "cloth flowing" | `[URL] hair gently swaying, soft breeze --motion low` |
| Cinemagraph | "minimal movement", "loop" | `[URL] eyes blinking, subtle movement --motion low --loop` |

## Genre-Specific Motion Presets

### Shonen

High energy, dynamic camera, action-focused.

```
[Image URL] dynamic tracking shot, action lines, high energy, impact frames --motion high
```

**Keywords**: action lines, impact frames, dynamic movement, speed effects

### Seinen

Atmospheric, slow pans, detailed and moody.

```
[Image URL] slow atmospheric pan, cinematic, detailed, moody lighting --motion low
```

**Keywords**: atmospheric, cinematic, slow pan, moody, subtle

### Shoujo

Soft, sparkly, romantic movement.

```
[Image URL] sparkle effects, soft focus, flowing hair, gentle movement, dreamy --motion low
```

**Keywords**: sparkle effects, soft focus, flowing, dreamy, gentle

### Slice of Life

Natural, peaceful, everyday movement.

```
[Image URL] gentle breeze, natural movement, peaceful atmosphere --motion low --raw
```

**Keywords**: gentle breeze, natural, peaceful, subtle

### Mecha

Mechanical precision, transformation sequences.

```
[Image URL] mechanical movement, precise motion, transformation, metallic --motion high
```

**Keywords**: mechanical, precise, transformation, metallic, hydraulic

### Horror

Unsettling, creeping movement, building tension.

```
[Image URL] creeping motion, shadows moving, subtle unease, slow zoom --motion low
```

**Keywords**: creeping, shadows, unease, slow, tension

## Loop Animation

Create seamless looping animations where the end frame matches the start.

### Basic Loop

```
[Image URL] gentle breathing, subtle movement --motion low --loop
```

### Cinemagraph-Style Loop

```
[Image URL] cinemagraph, single element moving, hair swaying, rest still --motion low --loop --raw
```

### Perfect Loop Tips

- Keep motion simple and cyclical
- Use `--raw` for more control
- Use `--loop` parameter (not text in prompt)
- Subtle movements loop better than dramatic ones
- Hair, cloth, and breathing work well
- Circular/orbital motions create natural loops

## Integrated Prompt Format

When using the anime-mj plugin with animation, you get TWO structured prompts:

1. **Image Prompt** - Full structured prompt with image parameters (`--niji 6`, `--ar`, `--sref`)
2. **Animation Prompt** - Same structure + `[Animation]` + `[Camera]` with video parameters only (`--motion`, `--loop`, `--raw`)

### Animation Sections

| Section | Content |
|---------|---------|
| `[Animation]` | What moves - flickering, swaying, breathing, glitching |
| `[Camera]` | Camera motion - orbits, pans, zooms, static |

### Animation Keywords by Style

| Style | Animation Keywords | Camera Keywords |
|-------|-------------------|-----------------|
| Action | speed lines, impact frames, dynamic movement | tracking shot, fast pan |
| Peaceful | gentle breeze, hair swaying, subtle breathing | static, slow zoom |
| Dramatic | flickering lights, emotional intensity | slow push, orbit |
| Horror | glitching, unsettling movement, shadows creeping | static, slow zoom |
| Loop | seamless motion, cyclical movement | static, orbit |

### Example: Cyberpunk Horror (Maximum Structure)

**Image Prompt:**
```
[Character] cyberpunk hacker, disheveled appearance, unsettling gaze | [Expression] hollow eyes, disturbing smile | [Pose] crouched over terminal | [Action] typing frantically | [Props] holographic displays, glitching screens | [Setting] neon-lit alley | [Background] dystopian cityscape | [Artist] Junji Ito | [Genre] cyberpunk horror | [Visual] bold high-contrast colors, neon pink and cyan --niji 6 --ar 2:3 --s 800 --sref 416523183 --sw 500
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] cyberpunk hacker, disheveled appearance, unsettling gaze | [Expression] hollow eyes, disturbing smile | [Pose] crouched over terminal | [Action] typing frantically | [Props] holographic displays, glitching screens | [Setting] neon-lit alley | [Background] dystopian cityscape | [Artist] Junji Ito | [Genre] cyberpunk horror | [Visual] bold high-contrast colors, neon pink and cyan | [Animation] neon signs flickering, holographic glitches, subtle breathing motion | [Camera] static --motion low --loop --raw
```

### Example: Magical Girl (Standard Structure)

**Image Prompt:**
```
[Character] magical girl, sparkling eyes | [Action] mid-transformation, graceful pose | [Scene] cosmic starry background | [Style] Sailor Moon, Naoko Takeuchi | [Genre] magical girl, shoujo | [Mood] soft pastel colors, glowing --niji 6 --ar 9:16 --s 600
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] magical girl, sparkling eyes | [Action] mid-transformation, graceful pose | [Scene] cosmic starry background | [Style] Sailor Moon, Naoko Takeuchi | [Genre] magical girl, shoujo | [Mood] soft pastel colors, glowing | [Animation] sparkles swirling, ribbons flowing, celestial glow pulsing | [Camera] camera orbits around --motion high --loop
```

## Integration with Anime-MJ Features

### With Artist Styles

Generate image with artist style, then animate using the integrated format:

**Image Prompt:**
```
[Character] warrior, dark armor | [Action] facing demon | [Scene] gothic cathedral | [Style] Berserk, Kentaro Miura | [Genre] dark fantasy | [Mood] dramatic shadows --niji 6 --ar 16:9 --s 800
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] warrior, dark armor | [Action] facing demon | [Scene] gothic cathedral | [Style] Berserk, Kentaro Miura | [Genre] dark fantasy | [Mood] dramatic shadows | [Animation] cape flowing, wind effects, atmospheric fog | [Camera] slow push --motion low
```

### With SREF Codes

SREF codes apply to image generation, not video. Generate styled image first:

**Image Prompt:**
```
[Character] forest spirit | [Action] floating | [Scene] enchanted forest | [Style] Studio Ghibli | [Genre] magical realism | [Mood] ethereal glow --niji 6 --ar 16:9 --sref 3408846050 --sw 300
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] forest spirit | [Action] floating | [Scene] enchanted forest | [Style] Studio Ghibli | [Genre] magical realism | [Mood] ethereal glow | [Animation] magical particles floating, gentle movement | [Camera] static --motion low --raw
```

### With Character Reference

Character reference is used during image generation, not video animation:

**Image Prompt:**
```
[Character] mage | [Action] casting spell | [Scene] magical forest | [Style] fantasy anime | [Genre] magical | [Mood] dramatic lighting --niji 6 --ar 16:9 --cref [URL] --cw 100
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] mage | [Action] casting spell | [Scene] magical forest | [Style] fantasy anime | [Genre] magical | [Mood] dramatic lighting | [Animation] magical energy swirling, robes flowing | [Camera] slow orbit --motion low
```

## Motion Level Guide

| Parameter | Effect | Best For |
|-----------|--------|----------|
| `--motion low` | Subtle, gentle movement | Cinemagraphs, peaceful scenes, character portraits, slow pans |
| `--motion high` | Dynamic, energetic movement | Action scenes, combat, dramatic moments, fast camera |

### Specifying Motion Level

Use the `--motion` parameter:

```
# Low motion (default, subtle)
[URL] subtle breathing, minimal movement, peaceful --motion low --raw

# High motion (dynamic, energetic)
[URL] explosive action, dynamic movement, high energy --motion high
```

## Example Prompts

The following examples use the integrated prompt format with `[Animation]` and `[Camera]` sections.

### Action Scene

**Image Prompt:**
```
[Character] warrior | [Action] mid-attack | [Scene] battlefield | [Style] shonen action | [Genre] action, dramatic | [Mood] intense lighting --niji 6 --ar 16:9 --s 700
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] warrior | [Action] mid-attack | [Scene] battlefield | [Style] shonen action | [Genre] action, dramatic | [Mood] intense lighting | [Animation] speed lines, impact frames, dynamic movement | [Camera] tracking shot --motion high
```

### Peaceful Moment

**Image Prompt:**
```
[Character] young girl | [Action] sitting by window | [Scene] sunlit room | [Style] slice of life | [Genre] peaceful | [Mood] soft warm lighting --niji 6 --ar 16:9 --s 400
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] young girl | [Action] sitting by window | [Scene] sunlit room | [Style] slice of life | [Genre] peaceful | [Mood] soft warm lighting | [Animation] gentle breeze through hair, soft fabric movement | [Camera] static --motion low --raw
```

### Seamless Loop

**Image Prompt:**
```
[Character] magical girl | [Action] peaceful pose | [Scene] starry background | [Style] shoujo | [Genre] magical | [Mood] soft glow --niji 6 --ar 1:1 --s 500
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] magical girl | [Action] peaceful pose | [Scene] starry background | [Style] shoujo | [Genre] magical | [Mood] soft glow | [Animation] hair gently swaying, soft breathing motion | [Camera] static --motion low --loop --raw
```

### Combat Impact

**Image Prompt:**
```
[Character] fighter | [Action] delivering punch | [Scene] arena | [Style] shonen | [Genre] combat | [Mood] dramatic shadows --niji 6 --ar 16:9 --s 800
```

**Animation Prompt** (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] fighter | [Action] delivering punch | [Scene] arena | [Style] shonen | [Genre] combat | [Mood] dramatic shadows | [Animation] impact frame, explosive energy, motion blur | [Camera] fast shake --motion high
```

## Tips for Better Animation

1. **Composition matters**: Images with clear subjects animate better
2. **Use --raw for control**: Reduces AI interpretation, follows prompt more closely
3. **Match motion to mood**: Action scenes need high motion, peaceful scenes need low
4. **Simple loops work best**: Complex motion is harder to loop seamlessly
5. **Front-facing works well**: Characters facing camera animate more naturally
6. **Consider aspect ratio**: Wider ratios (16:9, 21:9) suit cinematic motion
7. **Clean backgrounds help**: Busy backgrounds can create distracting movement

## Platform Note

Video generation is **only available on the Midjourney web app** at midjourney.com. You cannot generate videos through the Discord bot.

**Workflow**:
1. Generate anime image (Discord or web)
2. Copy image URL or use web app's "Animate" button
3. Create animation on web app
4. Download MP4 or GIF
