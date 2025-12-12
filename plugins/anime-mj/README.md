# Anime-MJ: Anime/Manga Prompt Builder

A Claude Code plugin that generates optimized anime and manga prompts for Midjourney's Niji mode. Features 30+ manga artists, 14 anime studios, 40+ curated SREF codes, and comprehensive genre support.

## Features

- **30+ manga/anime artists** with style keywords
- **14 anime studios** with distinct aesthetics
- **40+ curated SREF codes** for consistent visual styles
- **Character consistency workflows** with reference sheet templates
- **Video/animation support** with motion templates and camera guides
- **6 genre categories**: Shonen, Seinen, Shoujo, Slice of Life, Mecha, Horror
- **Full Niji 6 support** with all style modes
- **Interactive prompt building** with guided questions
- **Ready-to-paste output** for Discord/web

## Usage

### Slash Command

```
/anime-mj [description]
```

**Examples:**
```
/anime-mj a warrior in dark armor facing a demon
/anime-mj magical girl transformation sequence
/anime-mj cyberpunk hacker in neon-lit alley
/anime-mj slice of life school scene at sunset
```

### Interactive Flow

1. **Select genre** (Shonen/Seinen/Shoujo/Slice of Life/Mecha/Horror)
2. **Choose artist or studio style**
3. **Describe your subject**
4. **Select aesthetic vibe** (Cute/Dramatic/Scenic/Classic/Skip)
5. **Choose aspect ratio**
6. **Apply SREF code** (auto-matched based on your selections)
7. **Apply character reference** (optional - for character consistency)
8. **Animate image** (optional - for video/animation, web app only)
9. **Choose output structure** (Standard/Granular/Maximum)
10. **Output** structured, ready-to-use prompt

## Supported Artists

### Shonen
| Artist | Style |
|--------|-------|
| Akira Toriyama | Dragon Ball, dynamic action, bold colors |
| Eiichiro Oda | One Piece, exaggerated proportions, adventure |
| Masashi Kishimoto | Naruto, ninja themes, dynamic poses |
| Tatsuki Fujimoto | Chainsaw Man, chaotic, horror elements |
| Hajime Isayama | Attack on Titan, dark, dramatic |

### Seinen
| Artist | Style |
|--------|-------|
| Kentaro Miura | Berserk, dark fantasy, hyper-detailed |
| Katsuhiro Otomo | Akira, cyberpunk, dystopian |
| Naoki Urasawa | Monster, psychological thriller |
| Junji Ito | Horror, surreal, disturbing |
| Tsutomu Nihei | Blame!, biomechanical |

### Shoujo/Josei
| Artist | Style |
|--------|-------|
| Naoko Takeuchi | Sailor Moon, magical girl |
| CLAMP | Cardcaptor Sakura, elegant |
| Ai Yazawa | Nana, fashion-forward |

### Directors
| Creator | Style |
|---------|-------|
| Hayao Miyazaki | Studio Ghibli, whimsical |
| Makoto Shinkai | Your Name, photorealistic backgrounds |
| Hideaki Anno | Evangelion, mecha, psychological |
| Hiroyuki Imaishi | Trigger style, kinetic, explosive |

### Modern
| Artist | Style |
|--------|-------|
| Hirohiko Araki | JoJo, dramatic poses, baroque |
| Yusuke Murata | One Punch Man, hyper-detailed action |
| Sui Ishida | Tokyo Ghoul, watercolor-like |

## Supported Studios

| Studio | Aesthetic |
|--------|-----------|
| Studio Ghibli | Hand-painted, magical realism |
| Kyoto Animation | Moe, detailed eyes, fluid |
| MAPPA | Modern, dynamic action |
| Ufotable | CGI integration, vibrant |
| Trigger | Kinetic, exaggerated, colorful |
| Bones | Fluid animation, dynamic |
| Madhouse | Versatile, high quality |
| Shaft | Avant-garde, stylized |

## Model & Parameters

| Parameter | Description |
|-----------|-------------|
| `--niji 6` | Niji anime model (default, recommended for traditional anime) |
| `--v 7` | V7 native model (alternative, works well with SREF + anime keywords) |
| `--style raw` | Less stylized, more literal output (optional) |
| `--ar` | Aspect ratio |
| `--s` | Stylize (0-1000) |
| `--sref` | Style reference code |
| `--sw` | Style weight (0-1000) |
| `--cref` | Character reference |
| `--cw` | Character weight (0-100) |
| `--oref` | Omni reference (V7) |
| `--motion low` | Subtle video motion (web app only) |
| `--motion high` | Dynamic video motion (web app only) |
| `--loop` | Seamless video loop (web app only) |

## SREF Code Library

Curated SREF codes for consistent anime visual styles. See [docs/sref-library.md](./docs/sref-library.md) for full library.

### Quick Reference

| Category | Code | Name |
|----------|------|------|
| Ghibli | `3408846050` | Ghibli Vibes |
| Ghibli | `918084796` | Anime Serenity |
| Dynamic | `3730983883` | Dynamic Comic |
| Dark | `416523183` | Anime Vampire |
| Dark | `229704573` | Dark Warrior |
| Shoujo | `2178024008` | Iridescence |
| Retro | `16809792746` | 80s Retro |
| Modern | `65` | Neo-Noir |

### SREF Usage

```
# Basic usage
--sref 3408846050

# With style weight
--sref 3408846050 --sw 300

# Blend two codes
--sref 3408846050 918084796

# Weighted blend
--sref 3408846050::2 918084796::1
```

## What You Say → What You Get

The plugin builds detailed, **structured prompts** from simple inputs. Each section is labeled for easy modification:

### Simple Request
**You say**: "magical girl transformation, Sailor Moon style"
**You get**:
```
[Character] magical girl, adorable expression, big sparkling eyes | [Action] mid-transformation, dynamic graceful pose | [Scene] cosmic starry background, sparkles and ribbons spiraling | [Style] Sailor Moon, Naoko Takeuchi | [Genre] magical girl, shoujo, kawaii | [Mood] soft pastel colors, soft glowing lighting --niji 6 --ar 9:16 --s 600
```

### Dark Fantasy
**You say**: "warrior facing a demon, Berserk style"
**You get**:
```
[Character] lone warrior, black armor, intense expression | [Action] facing massive demon, battle stance | [Scene] gothic cathedral, atmospheric fog | [Style] Berserk, Kentaro Miura | [Genre] dark fantasy, seinen, dramatic | [Mood] hyper-detailed linework, bold high-contrast colors --niji 6 --ar 2:3 --s 800
```

### With Style Reference
**You say**: "peaceful meadow scene, Ghibli style, add a matching SREF"
**You get**:
```
[Character] young girl | [Action] walking through meadow | [Scene] sunlit meadow, wildflowers swaying | [Style] Studio Ghibli | [Genre] magical realism, cinematic | [Mood] soft golden hour lighting, atmospheric haze, peaceful --niji 6 --ar 16:9 --sref 3408846050 --sw 300
```

### Character Reference Sheet
**You say**: "create a character sheet for my mage character"
**You get**:
```
[Character] young female sorcerer, long silver hair, purple eyes, dark cloak | [Action] multiple views, front, side, three-quarter, back | [Scene] clean white background | [Style] anime | [Genre] character reference sheet | [Mood] full body, clear details --niji 6 --ar 16:9
```

### Structure Levels

The plugin offers three structure levels to match your editing needs:

| Level | Sections | Best For |
|-------|----------|----------|
| **Standard** | 6 sections | Most edits - balanced control |
| **Granular** | 8 sections | Fine-tuning details |
| **Maximum** | 10 sections | Full element control |

## Character Consistency

Create consistent characters across multiple images using reference sheets and the `--cref` parameter.

### Reference Sheet Types

| Type | Description |
|------|-------------|
| Multi-view | Front, side, 3/4, back views |
| Expression | Happy, sad, angry, surprised, neutral |
| Outfit | Same character, different clothes |
| Turntable | 360-degree rotation views |

### Character Weight Guide

| Weight | Effect | Use Case |
|--------|--------|----------|
| `--cw 100` | Full character (face + hair + clothes) | Same outfit, different pose |
| `--cw 50` | Moderate similarity | Different outfit, same character |
| `--cw 0` | Face only | Complete costume change |

### V6/V7 Compatibility

| Version | Parameter | Notes |
|---------|-----------|-------|
| V6 / Niji 6 | `--cref` | Primary method |
| V7 | `--oref` | Preferred in V7 |
| V7 | `--cref` | Still works but may be deprecated |

## Video/Animation

Animate your anime images using Midjourney's V1 video model. See [docs/video-animation.md](./docs/video-animation.md) for full guide.

> **Note**: Video generation requires the Midjourney **web app** (midjourney.com). Discord bot does not support video.

### Motion Styles

| Style | Description |
|-------|-------------|
| Action | Dynamic movement, speed effects, high energy |
| Slice-of-life | Gentle breeze, subtle movement, peaceful |
| Dramatic | Slow zoom, emotional moments, cinematic |
| Combat | Fast motion, impact frames, explosive |

### Camera Motion

| Motion | Description |
|--------|-------------|
| Pan | Horizontal/vertical tracking movement |
| Zoom | Push in or pull out |
| Orbit | Circular movement around subject |
| Static | Subtle character animation only |

### Animation Examples

When animating, the plugin outputs TWO prompts - one for image generation, one for animation with `[Animation]` and `[Camera]` sections:

**Action Scene (Maximum structure)**

*Image Prompt:*
```
[Character] cyberpunk hacker | [Expression] hollow eyes | [Pose] crouched | [Action] typing frantically | [Props] holographic displays | [Setting] neon-lit alley | [Background] dystopian cityscape | [Artist] Junji Ito | [Genre] cyberpunk horror | [Visual] bold colors --niji 6 --ar 2:3 --s 800 --sref 416523183 --sw 500
```

*Animation Prompt* (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] cyberpunk hacker | [Expression] hollow eyes | [Pose] crouched | [Action] typing frantically | [Props] holographic displays | [Setting] neon-lit alley | [Background] dystopian cityscape | [Artist] Junji Ito | [Genre] cyberpunk horror | [Visual] bold colors | [Animation] neon flickering, holographic glitches | [Camera] static --motion low --loop --raw
```

**Peaceful Loop (Standard structure)**

*Image Prompt:*
```
[Character] young girl | [Action] walking through meadow | [Scene] sunlit meadow | [Style] Studio Ghibli | [Genre] magical realism | [Mood] soft lighting --niji 6 --ar 16:9 --sref 3408846050 --sw 300
```

*Animation Prompt* (in web app, select the image you want to animate. In the "Animate Image" section choose "Animate Manually"):
```
[Character] young girl | [Action] walking through meadow | [Scene] sunlit meadow | [Style] Studio Ghibli | [Genre] magical realism | [Mood] soft lighting | [Animation] hair swaying, wildflowers dancing | [Camera] static --motion low --loop --raw
```

> **Model Note**: All examples use `--niji 6` (Niji mode). For V7 native, simply omit `--niji 6` or replace with `--v 7`. V7 produces excellent anime results when combined with SREF codes and anime-specific keywords.

## Documentation

- [SREF Code Library](./docs/sref-library.md) - Full curated library with 40+ anime style codes
- [Video/Animation Guide](./docs/video-animation.md) - Complete animation reference

## External Resources

- [sref-midjourney.com](https://sref-midjourney.com/style/niji) - 5,500+ SREF codes
- [Midlibrary.io](https://midlibrary.io/) - 2,800+ curated codes
- [promptsref.com/niji-6](https://promptsref.com/version/niji-6) - Niji-specific collection

## License

MIT
