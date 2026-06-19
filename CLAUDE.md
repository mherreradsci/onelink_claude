# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
yarn install        # Install dependencies
yarn dev            # Start dev server at http://localhost:3000
yarn build          # Build for production
yarn generate       # Generate static site
yarn preview        # Preview production build
```

## Architecture

**Onelink** is a link-in-bio tool where all profile data lives entirely in the URL — there is no backend or database. The data flow is:

1. **Editor (`pages/index.vue`)** — A two-column layout: left panel has the form, right panel shows a live preview via `<AppFormPreview>`. The user fills in profile data stored in a single `data` reactive object.
2. **Publish** — Clicking "Publish" encodes the `data` object to Base64 via `utils/transformer.js` (`encodeData`) and generates a URL like `/1?data=<base64>`. This URL is copied to clipboard.
3. **Public page (`pages/1.vue`)** — Reads the `?data` query param, decodes it with `decodeData`, and passes the result as `:acc` prop to `<TemplatesSimple>`.
4. **Template (`components/Templates/Simple.vue`)** — Renders the profile from the `acc` prop. The `/1` in the URL corresponds to this template; new templates would get new route numbers.

### Data schema (abbreviated keys to keep URLs short)

| Key | Field |
|-----|-------|
| `n` | Name |
| `d` | Description |
| `i` | Avatar image URL |
| `f/t/ig/gh/tg/l/e/w/y` | Social links (Facebook, Twitter, Instagram, GitHub, Telegram, LinkedIn, Email, WhatsApp, YouTube) |
| `ls` | Custom links array: `[{ l: label, i: icon, u: url }]` |

### Key conventions

- Icons use `nuxt-icon` with Phosphor (`ph:*`) icon names from the Iconify catalog.
- WhatsApp links are constructed as `https://wa.me/${acc.w}`; email as `mailto:${acc.e}`. All other social links are stored as full URLs.
- `vuedraggable` is used for reordering custom links in `AppForm/Links.vue`.
- No Pinia/Vuex — state is local `ref()` in `pages/index.vue` and passed down via props/v-model.
- Nuxt auto-imports: `ref`, `computed`, `useRoute` and all components are available without explicit imports.
