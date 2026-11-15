# web-agentic-pipeline

A [Claude Code](https://code.claude.com) plugin with a gated pipeline for static/Astro websites:

```
webpage-designer → HUMAN GATE 1 → webpage-coder → webpage-seo-reviewer (audit) → HUMAN GATE (SEO)
  → webpage-seo-reviewer (apply) → webpage-security-reviewer → HUMAN GATE 2 → webpage-publisher
```

| Agent | Model | Role |
|---|---|---|
| `webpage-designer` | sonnet | Writes the design plan, owns the design system. No code. |
| `webpage-coder` | sonnet | Implements an approved plan. Never commits. |
| `webpage-seo-reviewer` | sonnet | `audit` proposes SEO and freshness fixes; `apply` applies the ones the human approved. |
| `webpage-security-reviewer` | opus | Secrets, PII, unsafe code, dependency audit. Records PASS/FAIL and a fingerprint. |
| `webpage-publisher` | sonnet | Verifies, commits on a branch, opens the PR. Never merges or deploys. |

The `webpage-pipeline` skill orchestrates them and runs the human gates. Plans live in each
site's `docs/design/plans/`.

## Use it in a repo

Install it for yourself:

```
/plugin install web-agentic-pipeline --marketplace molayab/web-agentic-pipeline
```

Or enable it for everyone working in a repo by adding to its `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "molayab": { "source": { "source": "github", "repo": "molayab/web-agentic-pipeline" } }
  },
  "enabledPlugins": { "web-agentic-pipeline@molayab": true }
}
```

Then give the repo a `## Pipeline profile` section in its `AGENTS.md` (the agents read it):

```markdown
## Pipeline profile

- Default branch / deploy: …
- Build: `npm run build`; URLs that must exist in the output: …
- Canonical host: …
- Pages registry (sitemap/hreflang source): …
- Locales: …
- Legal pages (never edited by agents): …
- Feeds and their consumers: …
- Sources of truth for repeated facts (versions, prices, features): …
- Public by design (analytics IDs, beacon tokens, store URLs): …
- Intentional public contacts: …
- External content origins allowed in links: …
- Vendored files / non-registry dependencies: …
- Attribution policy: …
```

Run it with `/web-agentic-pipeline:webpage-pipeline <idea>`.

## Releasing

Bump `version` in `plugins/web-agentic-pipeline/.claude-plugin/plugin.json`, commit, and tag
(`vX.Y.Z`). Keep agents site-agnostic: anything specific to one site belongs in that site's
profile.
