# supermuseum-boxart — Agent Reference

Static boxart CDN. `boxart/` (5,600+ `.webp` files, ~534 MB) deploys as the site root of Cloudflare Pages project `supermuseum-boxart` → `https://supermuseum-boxart.pages.dev/` (e.g. `boxart/gb/X.webp` serves at `/gb/X.webp`). No build, no `wrangler.toml`, no `package.json`. `boxart/_headers` sends `Access-Control-Allow-Origin: *` + `Cache-Control: public, max-age=604800` (mandatory for `crossorigin="anonymous"` imgs; replacements lag ≤7 days for returning visitors).

## Tooling: MCP first, CLI second

Both MCP servers are connected via the OpenChamber harness — prefer them over `npx`/CLI (no tokens, no version drift).

- **Cloudflare MCP** (`tools.cloudflare.*`): `pages_list`, `pages_get`, `pages_deployments_list`, `pages_deployments_create`, `pages_domains_list` on account `livercake heavy industries, inc.`, project `supermuseum-boxart`. First choice for status checks and deploys.
- **Netlify MCP**: team `livers and cakes`, project `supermuseum` — for the site UI repo, not this one.
- **CLI fallback only**: `cf pages deploy` (preferred, no Wrangler config here) or `npx -y wrangler pages deploy boxart/ --project-name supermuseum-boxart --branch main`.

## Workflows

- Add: convert to WebP (sharp), `cp` into `boxart/{platform}/`, `git add` + commit + `git push origin main`, redeploy Pages via MCP (fallback CLI above). Verify live bytes with `?cb=$RANDOM` (max-age caching reads like a failed deploy).
- Remove: `git rm boxart/{platform}/SERIAL*.webp`, commit, push, redeploy. Old URL 404s after deploy lands.
- Never push without the user's say-so — stage and ask unless pre-authorized.
