# sitio/ — the portfolio published at opptus.com

⚠️ **Everything in this folder is public.** The deploy workflow publishes the *entire* directory, so
each file here becomes a public asset at `opptus.com/<name>`. Dropping a file in hosts it — never put
drafts, client material, or anything private here.

`index.html` is a **single self-contained HTML file** — all CSS (inline `<style>` with CSS custom
properties under `:root`) and JS (inline `<script>`) live in that one file. No build step; open it
directly in a browser to preview.

- **Theme:** light "Costa luminosa" palette (sand/ocean) — nature-inspired identity, edit the `:root`
  variables to retune. All component colors derive from those vars.
- **Brand title:** "Consultor & AI Builder" (consultant + builder positioning — strategy *and*
  execution). Keep `<title>`, OG/Twitter, and footer in sync if it changes.
- **Sections:** hero (with photo `foto.jpg` + OG share image), about (4 result-oriented cards),
  process (`#process`, "Cómo trabajo" — the 4-step consulting engagement: Diagnóstico → Diseño →
  Implementación → Acompañamiento), projects (split into two `.project-group`s: *Empresa* and
  *Personales*), education, contact. **No stack section** — removed as not relevant for the target
  client.
- **Hero structure:** photo → badge → `<h1>` name → `.hero-role` ("Consultor & AI Builder") →
  `.hero-sub` paragraph → CTA buttons → stats.
- **Projects — Empresa group:** the group title is just "Proyectos Empresa"; client name
  ("Agroberries") appears as a `.project-client` sub-label below the title. This pattern allows adding
  future clients without restructuring — just add another `.project-client` + grid block inside the
  same `.project-group`.
- **`<head>`** carries SEO + Open Graph + Twitter meta and an inline-SVG favicon; `og:image`/
  `twitter:image` point to `https://opptus.com/foto.jpg` — that filename is a public URL, so renaming
  it means updating the two meta tags, the hero `<img>` and the workflow's validation step together.
- **`foto.jpg`** (1200×675, q88, ~126 KB) is both the hero avatar (rendered at 116 px) and the social
  share image. Keep it a JPEG: it's a photograph, and PNG can only shrink it by posterizing skin tones.
- **`opptus-logo.png`** is served at `https://opptus.com/opptus-logo.png` and is what the email
  signature (`canales/email/firma_opptus.html`) loads. It is also the source the business-card master
  is derived from.

## Bilingual (ES/EN) system — the most important thing to understand

Spanish is the default (`currentLang = 'es'`). The EN ⇄ ES toggle (`toggleLang()`) swaps text by
looking up a key in the `translations` object, which has parallel `es` and `en` maps. Translatable
elements are marked with one of three attributes, each handled differently:

- `data-i18n` → replaced via `textContent` (plain text).
- `data-i18n-html` → replaced via `innerHTML` (copy containing `<strong>`, `<br/>`, etc.).
- `data-i18n-badge` → special-cased to preserve the status-dot `<span>` and only swap the trailing
  text node.

**When adding or changing any visible text you must:**

1. Mark the element with the correct `data-i18n*` attribute and a unique dot-namespaced key (e.g.
   `project.closeops.desc`).
2. Add that key to **both** `translations.es` and `translations.en` — an unmatched key silently
   leaves the element untranslated on toggle.
3. Write the Spanish version directly in the HTML (it is the default rendered state) and ensure the
   `es` entry matches it.

**Language persistence:** `applyLang(lang)` renders, `toggleLang()` persists to `localStorage`, and an
IIFE on load restores the saved language.

## Deploy (auto-publish to GitHub Pages)

The live site is a **separate repo** (`joseantonioochagavia.github.io`, branch `main`, path `/`); the
source of truth lives here. Publishing is automated by **`.github/workflows/publish-portfolio.yml`**:
on push to `main` touching `sitio/**` (plus a `workflow_dispatch` backup), it publishes the entire
`sitio/` directory (`publish_dir: ./sitio`) to the Pages repo via `peaceiris/actions-gh-pages` using
the **`PAGES_DEPLOY_KEY`** deploy-key secret. The live site updates in ~1-3 min.

**Human workflow:** branch → edit `sitio/` → review → merge to `main` → it publishes itself. No manual
upload. Full design + key-rotation steps in
`operacion/registros/2026-06-23_publicacion_automatica_portafolio.md`.

**Custom domain:** served at **`opptus.com`** (registered at Porkbun; DNS = 4 GitHub Pages A records at
apex + `www` CNAME → `joseantonioochagavia.github.io`, which redirects to the apex). Because the
workflow deploys with `keep_files: false` (wipes the Pages repo each run), the domain is kept alive by
the **`cname: opptus.com`** input on the peaceiris step — it rewrites the `CNAME` file on every
publish. If the public domain ever changes, update that `cname:` value (a bare `CNAME` file set only
via the GitHub UI would be deleted on the next deploy). Setup record:
`operacion/registros/2026-07-01_dominio_opptus.md`.
