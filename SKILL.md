---
name: content-creation
description: "Full-stack content creation pipeline: from idea to published blog post, video, and social media distribution. Orchestrates research, storytelling, visual assets, Remotion video, and social copy into a complete content package. Use when: (1) creating a new blog post, case study, or writing entry, (2) turning an idea or experience into a structured narrative, (3) generating visual content (screenshots, GIFs, video) to accompany a post, (4) creating social media content (X threads, Instagram carousels) from long-form writing, (5) packaging and distributing content across platforms. Triggers on: 'new post', 'blog post', 'case study', 'content creation', 'write about', 'create content', 'content pipeline', 'publish post', 'social content from post'."
---

# Content Creation Pipeline

Six-phase workflow: idea → published multimedia content package with social distribution.

```
RESEARCH → NARRATIVE → VISUAL ASSETS → VIDEO → SOCIAL → DEPLOY
```

## Phase 1: Research

Gather evidence, capture production state, pull metrics. Never write without data.

**Compounding skills:** `/deep-research`, `/agent-browser`, `/competitor-intel`, curl/API data pulls.

**Checklist:**
- [ ] Core claim identified (what are you proving?)
- [ ] 3-5 validating data points gathered
- [ ] Production screenshots captured (full-page + detail)
- [ ] Metrics pulled from APIs (never fabricate numbers)
- [ ] Target audience and their concerns identified

## Phase 2: Narrative

Structure using a proven framework. See [references/storytelling.md](references/storytelling.md) for full guides.

| Content Type | Framework | Structure |
|-------------|-----------|-----------|
| Case study | PSI | Challenge → Solution → Quantified results |
| Industry highlight | ABT | Context AND, BUT challenge, THEREFORE outcome |
| Technical deep dive | 1-3-1 | One idea, three evidence points, one takeaway |
| Product launch | Pixar Spine | Once upon a time... Every day... Until one day... |
| Data story | Data Arc | Context → Tension → Resolution |

### Blog Post Structure

```
frontmatter (title, summary, date, published, tags)
Hook (1-2 sentences — open loop or surprising claim)
Hero media (video or key image)
Numbers section (table with headline metrics)
Problem section + image
Solution section + image + progressive detail image
Evidence section + dashboard screenshots + data visualizations
Context section + variant screenshots
Practice section + animated GIF
Generalization section
Closing (memorable one-liner)
```

**Rules:** Lead with numbers. One image per ~300 words. Bold key terms on first use. 3-4 sentence paragraphs max. Use `<video>` for MP4, `![alt](path)` for images/GIFs.

**Output:** MDX file at `apps/chat/content/writing/{slug}.mdx`.

## Phase 3: Visual Assets

**Compounding skills:**
- `/agent-browser` — production screenshots, UI workflows
- `/pencil` (MCP) — design social cards, diagrams, slides in `.pen` files
  - `get_guidelines(topic)` for design-system/landing-page/slides guidance
  - `get_style_guide(tags)` for visual consistency
  - `batch_design` for multi-element compositions
  - `get_screenshot` to export assets
- `/before-and-after` — visual diffs for transformation stories
- `/frontend-design` — custom visual components
- `/arcan-glass` — BroomVA brand styling

### AI-Generated Assets

See [references/ai-video-generation.md](references/ai-video-generation.md) for full API details, code examples, and Remotion integration patterns.

**Image generation (Nano Banana / Gemini):**
```bash
# CLI: quick hero images, social cards, diagrams
nano-banana "A hero image for {topic}, dark theme, glass effects, 1080x1080"
# SDK: @google/genai with model "gemini-3.1-flash-image" (Nano Banana 2)
# MCP: @aeven/nanobanana-mcp for Claude Code integration
```

**Video generation (Veo 3.1):**
```bash
# MCP server for Claude Code
uvx mcp-veo3 --output-dir assets/ai-clips/
# SDK: @google/genai with model "veo-3.1-generate-preview"
# Capabilities: 4K, native audio, image-to-video, frame interpolation
# Duration: 4-8s per clip, chain up to 20 extensions (~148s)
```

**Multi-provider (fal.ai):**
```bash
# Single API for Veo 3.1, Sora 2 Pro, Kling 3 Pro, 600+ models
bun add @fal-ai/client
# Swap models by changing endpoint string, no code changes
```

**Preprocessing AI clips for Remotion (critical):**
```bash
ffmpeg -i ai_clip.mp4 -c:v libx264 -crf 18 -movflags +faststart -r 30 processed.mp4
```

### Manual Asset Pipeline

**Image pipeline:**
```bash
magick input.png -resize 1200x -quality 85 output-opt.png
magick f1.png f2.png f3.png -resize 1200x675! -set delay 200 -loop 0 flow.gif
mkdir -p apps/chat/public/images/writing/{slug}/
```

**Naming:** `{subject}-{descriptor}-opt.png`

**Checklist:** Hero image/video, 1 image per section (5-7 min), 1+ animated GIF, all < 500KB, descriptive alt text.

## Phase 4: Video

**Compounding skills:** `/remotion-best-practices` — read rules for animations, sequencing, transitions, images, text.

**Video structure (15-30s):**
```
Title (3-4s) → Stats (3s) → Screenshots (2-3s each) → Workflow (3-4s) → Closing (3-4s)
```

**Key Remotion rules:** Use `Img` + `staticFile()` (never `<img>`). Use `spring()` for organic motion. Use `Sequence` with `premountFor`. No CSS transitions or Tailwind animation classes.

### Hybrid AI + Remotion Pipeline

Combine AI-generated footage with Remotion motion graphics for production-quality output:

```
Nano Banana → hero images, backgrounds, social cards
Veo 3.1 → cinematic B-roll clips (8s each, 4K, with audio)
Remotion → motion graphics, titles, transitions, data viz
FFmpeg → preprocess AI clips, final GIF conversion
```

**In Remotion compositions:**
```tsx
// AI-generated video as background layer
<OffthreadVideo src={staticFile("assets/veo-clip.mp4")} style={{ objectFit: "cover" }} />

// AI-generated image
<Img src={staticFile("assets/nano-banana-hero.png")} />

// Dynamic duration from AI clips
// Use @remotion/media-parser parseMedia() with calculateMetadata
```

**Use `<TransitionSeries>` from `@remotion/transitions`** to blend AI clips with motion graphics scenes via fade/wipe/slide transitions.

### Render Commands

```bash
cd /tmp/{project}-remotion && bun install
npx remotion render {Id} --output out/video.mp4
ffmpeg -y -i out/video.mp4 -vf "fps=12,scale=960:-1:flags=lanczos" -c:v gif out/video.gif
```

## Phase 5: Social Distribution

See [references/social-distribution.md](references/social-distribution.md) for copy patterns and [references/social-publishing.md](references/social-publishing.md) for CLI/MCP tool setup.

### X Thread (5-8 tweets)
1. **Hook** — surprising stat, contrarian claim, or earned insight (50% of effort here)
2. **Context** — set the scene
3-6. **Key insights** — one per tweet, image every 2-3 tweets
7. **Strongest evidence**
8. **CTA** — link, follow, or question

**Publishing:** Use `xurl` CLI or Twitter MCP server to post directly.
```bash
xurl post "1/7 — [Hook tweet text]"
xurl media upload hero-image.png  # returns MEDIA_ID
xurl post "2/7 — [Context]" --media-id MEDIA_ID
```

### Instagram Carousel (8-12 slides, 1080x1350px)
Use `/pencil` to design slides. Cover → Problem → Insights (1/slide) → Stat → Summary → CTA.

**Publishing:** Use Instagram MCP server (ig-mcp) or Meta Graph API.

### LinkedIn Post
Hook in first 210 chars. 2-3 paragraphs + bullet list + CTA. 3-5 hashtags.

**Publishing:** Use LinkedIn MCP server (linkedin-mcp) or REST API with OAuth token.

## Phase 6: Deploy

```bash
git checkout -b content/{slug}
git add apps/chat/content/writing/{slug}.mdx apps/chat/public/images/writing/{slug}/
git commit -m "content: add {title}"
git push -u origin content/{slug}
gh pr create --title "content: {short title}" --body "..."
```

## Dependency Map

```
┌─ RESEARCH ──────────────────────────────────────────────────────┐
│  /deep-research    /agent-browser    /competitor-intel    curl   │
├─ AI GENERATION ─────────────────────────────────────────────────┤
│  Nano Banana (@google/genai)   Veo 3.1 (@google/genai)          │
│  fal.ai (@fal-ai/client)      ElevenLabs (voiceover)            │
│  nano-banana CLI               mcp-veo3 (MCP server)            │
│  @aeven/nanobanana-mcp         veo-mcp-server                   │
├─ DESIGN ────────────────────────────────────────────────────────┤
│  /pencil (MCP)   /before-and-after   /frontend-design           │
│  /arcan-glass    magick/ffmpeg                                   │
├─ VIDEO ─────────────────────────────────────────────────────────┤
│  /remotion-best-practices   /skills-showcase   /json-render-remotion │
│  @remotion/media-parser     @remotion/transitions                │
├─ NARRATIVE ─────────────────────────────────────────────────────┤
│  references/storytelling.md   references/social-distribution.md  │
│  references/visual-content.md                                    │
├─ PUBLISH ───────────────────────────────────────────────────────┤
│  xurl (X CLI)    twitter-mcp-server    linkedin-mcp              │
│  ig-mcp          Ayrshare MCP (multi-platform)                   │
├─ DEPLOY ────────────────────────────────────────────────────────┤
│  git + gh CLI    /vercel-cli    Vercel preview CI/CD             │
└─────────────────────────────────────────────────────────────────┘
```

## Reference Files

- [references/storytelling.md](references/storytelling.md) — narrative frameworks with examples
- [references/visual-content.md](references/visual-content.md) — image placement, optimization, GIF vs video
- [references/social-distribution.md](references/social-distribution.md) — platform copy patterns, carousels, atomization
- [references/social-publishing.md](references/social-publishing.md) — CLI tools, MCP servers, OAuth setup for X, LinkedIn, Instagram
- [references/ai-video-generation.md](references/ai-video-generation.md) — Nano Banana, Veo 3.1, Remotion integration, fal.ai multi-provider
