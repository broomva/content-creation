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

```bash
cd /tmp/{project}-remotion && bun install
npx remotion render {Id} --output out/video.mp4
ffmpeg -y -i out/video.mp4 -vf "fps=12,scale=960:-1:flags=lanczos" -c:v gif out/video.gif
```

## Phase 5: Social Distribution

See [references/social-distribution.md](references/social-distribution.md) for full platform guides.

### X Thread (5-8 tweets)
1. **Hook** — surprising stat, contrarian claim, or earned insight (50% of effort here)
2. **Context** — set the scene
3-6. **Key insights** — one per tweet, image every 2-3 tweets
7. **Strongest evidence**
8. **CTA** — link, follow, or question

### Instagram Carousel (8-12 slides, 1080x1350px)
Use `/pencil` to design slides. Cover → Problem → Insights (1/slide) → Stat → Summary → CTA.

### LinkedIn Post
Hook in first 210 chars. 2-3 paragraphs + bullet list + CTA. 3-5 hashtags.

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
├─ DESIGN ────────────────────────────────────────────────────────┤
│  /pencil (MCP)   /before-and-after   /frontend-design           │
│  /arcan-glass    magick/ffmpeg                                   │
├─ VIDEO ─────────────────────────────────────────────────────────┤
│  /remotion-best-practices   /skills-showcase   /json-render-remotion │
├─ NARRATIVE ─────────────────────────────────────────────────────┤
│  references/storytelling.md   references/social-distribution.md  │
│  references/visual-content.md                                    │
├─ DEPLOY ────────────────────────────────────────────────────────┤
│  git + gh CLI    /vercel-cli    Vercel preview CI/CD             │
└─────────────────────────────────────────────────────────────────┘
```

## Reference Files

- [references/storytelling.md](references/storytelling.md) — narrative frameworks with examples
- [references/visual-content.md](references/visual-content.md) — image placement, optimization, GIF vs video
- [references/social-distribution.md](references/social-distribution.md) — platform copy patterns, carousels, atomization
