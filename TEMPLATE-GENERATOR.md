# TEMPLATE-GENERATOR.md — Deterministic studio generator

A zero-dependency Node script that turns one structured studio brief into static demo
HTML from a reusable template engine. It is the **fast, deterministic** path that
complements the AI-variation workflow in `GENERATION-WORKFLOW.md`.

## When to use which

Both paths start from a studio brief; they differ in how the design is produced.

- **AI-variation workflow** (`GENERATION-WORKFLOW.md`) — hand the prose brief to several AI
  tools, each designs a fresh variant, you pick the best. Use it for **bespoke, exploratory**
  design when you want genuinely different directions per studio. Variation comes from the models.
- **Deterministic generator** (this doc) — fill a JSON brief, run one command, get HTML from a
  proven template. Use it for **fast, consistent spin-up** when you already have a design you
  trust and just want to drop a new studio's content into it. Variation comes from the brief, not
  the model.

They coexist. A design first explored via the AI workflow can later be hardened into a template
engine here so the next studio on that direction is one command away.

## Run it

```
npm run generate -- <studio-slug>            # all engines for one studio
npm run generate -- <studio-slug> <engine>   # one engine only
npm run generate:all                         # every brief × every engine
```

Output: `studios/<slug>/demos/demo-N-<engine>/index.html`. Generated demos are written on
demand and are **not** auto-registered in the studio's `templates.json` gallery manifest — review
the output, then add a manifest entry (and a preview PNG) when you want it to appear in the gallery.

## Inputs

- `studio-briefs/<slug>.json` — the canonical, machine-readable brief: identity, contact, links,
  brand tokens, asset paths, and section copy (hero, classes, faculty, testimonials, FAQ, etc.).
  One brief per studio, the single source of truth the generator reads. See
  `studio-briefs/absolute-dance.json` for a worked example.
- `template-engines/<engine>/template.html` — a self-contained HTML template with Mustache-style
  placeholders (`{{var}}`, `{{{raw}}}`, `{{#each}}`, `{{#if}}`, dotted paths, registered helpers).
- `template-engines/<engine>/engine.json` — engine metadata (id, label, demo number, order,
  GHL-compatibility, notes).

Assets are not copied; the brief points at paths already under `studios/<slug>/assets/`.

## Add a new engine

1. Create `template-engines/<your-engine>/template.html` using the placeholder syntax.
2. Add `template-engines/<your-engine>/engine.json` with a unique `id` and a `demoNumber`/`order`.
3. If the design needs computed bits (icon sets, generated spans), extend `buildHelpers()` in
   `scripts/generate-studio.mjs`.
4. Run `npm run generate -- <studio-slug> <your-engine>` and review the output.

The bundled `editorial-storyteller` engine is the reference example.
