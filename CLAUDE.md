# CLAUDE.md — House of Bespoke · Coming Soon (KSA)

Context and guidance for working on this project. Read this first.

---

## What this is
A single static "Coming Soon" landing page for **House of Bespoke** — a luxury
bespoke men's tailoring house launching in the **Kingdom of Saudi Arabia**
(Al Khobar). One page, no build step, no framework. The whole site is
`index.html` + the `assets/` folder.

- **Live:** https://www.houseofbespoke.co  (apex `houseofbespoke.co` → 308 redirect → `www`)
- **Repo:** `spatialtechlab-max/hilton-mtm-coming-soon` (GitHub, **public**)
- **Host:** Vercel project `hilton-mtm-coming-soon` (auto-deploys from `main`)

---

## Brand
| | |
|---|---|
| Name | House of Bespoke |
| Tagline | Luxury Bespoke Men's Tailoring |
| Message | Coming Soon — to the Kingdom of Saudi Arabia |
| Location | Al Khobar · Kingdom of Saudi Arabia |
| Phone / WhatsApp | +966 54 425 5624 |
| Instagram | https://www.instagram.com/houseofbespoke_ksa  (`@houseofbespoke_ksa`) |

### Palette (sampled from the crest — `assets/logo.png`)
Defined as CSS variables in `:root`:
```
--green-deep:  #06231d   --gold:        #d8b878
--green:       #0c3a30   --gold-bright: #f0d9a4
--green-soft:  #135043   --gold-deep:   #b08d4f
--ink:         #eef1ea
```
Deep emerald + gold. Keep all new UI on this palette.

### Type (self-hosted in `assets/fonts/`, no CDN)
- **Cormorant Garamond** (serif) — headline "Coming Soon", italic subhead, wordmark
- **Montserrat** (sans) — tracked uppercase labels, buttons, footnote

Fonts are self-hosted on purpose (latin `woff2` + `assets/fonts/fonts.css`) so the
page renders identically everywhere, including offline and inside KSA, with zero
external dependency. Don't re-add Google Fonts `<link>` tags.

---

## File structure
```
hilton-mtm-coming-soon/
├── index.html              ← the entire site (HTML + inline CSS)
├── CLAUDE.md               ← this file
├── .gitignore              ← keeps local preview PNGs out of git
└── assets/
    ├── logo.png            ← House of Bespoke crest (also the favicon / OG image)
    ├── hero-1.jpg          ← DSCF5036 — chalk + rule fabric marking
    ├── hero-2.jpg          ← DSCF5034 — chalk + rule fabric marking (CURRENT backdrop)
    ├── hero-3.jpg          ← DSCF5107 — hand pad-stitched canvas + thimble ring
    └── fonts/              ← self-hosted woff2 + fonts.css
```
Hero photos are optimized to ~1920px wide, ~350–400 KB each (heavy blur hides
compression). Originals were ~9 MB camera JPGs.

---

## Design / layout notes
- **Single blurred backdrop:** currently `hero-2.jpg` only (`#bgA`), blurred 20px,
  darkened, emerald-tinted, with a slow Ken-Burns drift. `hero-1` / `hero-3` are
  kept in `assets/` and were previously used in a 3-image crossfade — see git
  history if you want to bring the rotation back.
- **Layers (back → front):** `.bg-photo` (blurred image) → `.bg-gradient`
  (emerald tint that keeps gold text legible) → `.bg-texture` (fine pinstripe +
  vignette) → `.frame` (content).
- **Entrance:** staggered fade-up (`.reveal .d1…d7`) + a gold shimmer sweep on the
  headline. All animation is disabled under `prefers-reduced-motion`.
- **Responsive:** `@media (max-width: 560px)` stacks the contact buttons and shrinks
  type; `@media (max-height: 780px)` compresses the crest/spacing so it fits laptop
  and landscape heights without scrolling.

---

## ⚠️ Gotchas already fixed — don't reintroduce
1. **Vertical centering must use `margin: auto` on `.frame`, NOT
   `justify-content: center` on `body`.** With flex `justify-content: center`, when
   content is taller than the viewport it gets pushed off the top *and becomes
   unscrollable* (the crest got clipped on laptops). `margin: auto` collapses to 0
   on overflow, so the top stays reachable. `body` also must use `min-height`, never
   a fixed `height: 100%`.
2. **Gold gradient text needs roomy `line-height` + `padding-bottom`.** The headline
   uses `-webkit-background-clip: text`, which only paints the gradient inside the
   text's box. With a tight `line-height` the "g" descender fell *outside* that box
   and looked cut off. Fixed with `line-height: 1.18` + `padding-bottom: 0.12em` on
   `.headline`. Keep that breathing room if you restyle the headline.

---

## How to make common changes
- **Swap the backdrop photo:** change the `url(...)` on `#bgA` (or re-enable the
  multi-image crossfade from git history). Keep new images blurred + tinted so the
  gold text stays legible.
- **Change copy:** edit the text in `index.html` (`.tagline`, `.headline`,
  `.subhead`, `.location`, `.contact`, `.footnote`). It's plain HTML.
- **Add a launch date / email capture / more socials:** add into the `.contact`
  block or just below it, styled with the existing `.contact a` / `.ghost` classes.
- **Recolor:** edit the `:root` variables; everything references them.

### Local preview
No server needed — open `index.html` in a browser, or:
```
python3 -m http.server 9420   # then visit http://localhost:9420
```

---

## Deployment

**Flow:** push to `main` on GitHub → Vercel auto-builds & deploys to production
(`houseofbespoke.co`). It's a zero-config static deploy — Framework = "Other", no
build command, no output directory.

### ⚠️ The repo MUST stay public (Vercel Hobby plan)
The Vercel project is on the **free Hobby plan**. Hobby will **block** any deploy
whose commit author is not the project owner **when the repo is private**
("Deployment Blocked — Hobby Plan does not support collaboration for private
repositories"). Symptom: pushes land on GitHub but the live site never updates and
the Deployments panel shows a red ✗ "Blocked".

**Resolution used:** the repo is **public**, which lets Hobby accept commits from any
author. If the client ever needs it private again, either upgrade Vercel to **Pro**
(and add the committer as a team member) or commit as the project owner's account.

### Verify a deploy actually went live
Don't trust the dashboard alone — check the served file:
```
curl -s "https://www.houseofbespoke.co/?cb=$RANDOM" | grep "line-height: 1.18"
```

---

## Domain & DNS (GoDaddy → Vercel)
DNS is managed at **GoDaddy** (nameservers `*.domaincontrol.com`); only two records
point at Vercel — everything else (Microsoft 365 / GoDaddy **email**: `MX`,
`autodiscover`, `email`, `msoid`, `sip`, `lyncdiscover`, all `TXT`/`SPF`/`DMARC`,
`SRV`) is **untouched and must stay that way**.

| Type | Name | Value |
|------|------|-------|
| `A` | `@` | `216.198.79.1` (Vercel) |
| `CNAME` | `www` | `8474b7da6d7e468b.vercel-dns-017.com` (Vercel) |

`www` is primary; the apex 308-redirects to it (set in Vercel "Add Domain" with
"Redirect apex domains to www"). SSL is auto-issued by Vercel. A second domain,
`houseofbespoke.in`, exists in the same GoDaddy account but is **not** connected.

---

## Conventions
- Static only — no framework, no build step, no external runtime dependencies.
- Match the existing inline-CSS style and the emerald/gold design language.
- Commit messages: short and descriptive. (No AI/co-author attribution in commits.)
