# Demo UI

!!! warning "Testnet preview — not tested"
    The Demo UI runs against the **Salvium RC2 testnet** and has not been tested.
    It is provided for exploration and developer feedback only.
    **Do not use with real assets or treat any output as production-ready.**

The **Salvium RWA/NFT Demo** is a single-page application that shows the full token lifecycle — browse a marketplace of existing tokens, create new ones, and view token details — all backed by a real Salvium testnet.

**Live demo:** [demo.salvium.io](https://demo.salvium.io)

---

## What the demo shows

```
User fills form → Meta Generator produces JSON → SDK encodes to hex →
Middleware proxies to wallet-rpc → Token created on-chain →
Marketplace reflects new asset
```

This is a **proof of concept**, not a production marketplace. It demonstrates that a real-world asset or NFT can be represented and minted on Salvium with structured, verifiable metadata.

---

## Demo mode vs live mode

The demo UI works in two modes:

| Mode | When | What you see |
|------|------|-------------|
| **Demo mode** | GitHub Pages / no local middleware | Sample tokens (PROP, NFT1, INV1, GOV1) with a yellow warning banner |
| **Live mode** | Local middleware running on port 3001 | Real tokens from your local Salvium testnet |

The app detects which mode it's in automatically on startup (2-second connection check).

---

## Screens

### Screen 1 — Marketplace

The landing page shows a grid of all tokens in the wallet. Each card shows:

- Ticker and token type (NFT, RWA, Invoice, Governance)
- Name and supply
- A **demo** badge when in demo mode

Click any card to go to the token detail page.

### Screen 2 — Create Asset

A form with four pre-filled templates:

| Template | Standard | Pre-fills |
|----------|----------|-----------|
| Property Token | ERC-3643 | GB real estate fields |
| NFT | ERC-721 | Image URI, attributes |
| Invoice Token | ERC-1400 | Invoice valuation |
| Governance Proposal | CIP-108 | Proposal text, voting params |

Fill in the ticker (exactly 4 characters), name, and any other fields, then click **Create Token**.

!!! warning "Create requires local middleware"
    If no middleware is detected, the Create button is disabled and setup instructions are shown. The public demo on GitHub Pages is read-only.

### Screen 3 — Token Detail

Shows on-chain data returned by `get_token_info`:

- Asset type, name, supply, version
- Metadata URL (when set)
- Network status

---

## Running the full stack locally

You need four things running:

=== "1. Daemon"

    ```bash
    salviumd --testnet --offline --fixed-difficulty 500
    ```

=== "2. Wallet RPC"

    ```bash
    salvium-wallet-rpc \
      --wallet-file ./demo \
      --password demo \
      --testnet \
      --daemon-address 127.0.0.1:29081 \
      --rpc-bind-port 29088 \
      --disable-rpc-login
    ```

=== "3. Middleware"

    ```bash
    cd salvium/middleware
    RPC_URL=http://127.0.0.1:29088/json_rpc \
    RPC_USER=1 \
    RPC_PASS=1 \
    PORT=3001 \
    npx tsx src/server.ts
    ```

=== "4. Demo UI"

    ```bash
    cd salvium/demo-ui
    npm run dev
    ```

    Open `http://localhost:5174`

---

## Configuration

The demo UI reads the middleware URL from an environment variable:

```bash
# .env.local
VITE_API_URL=http://127.0.0.1:3001
```

If `VITE_API_URL` is not set, it defaults to `http://127.0.0.1:3001`.

To point the UI at a hosted middleware (for a shared demo):

```bash
VITE_API_URL=https://your-middleware.railway.app npm run build
```

---

## Deploying to GitHub Pages

The demo uses a GitHub Actions workflow that builds and deploys automatically on every push to `main`:

```yaml
# .github/workflows/deploy.yml
on:
  push:
    branches: [main]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - run: npm ci && npm run build
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with: { path: ./dist }
  deploy:
    needs: build
    environment: { name: github-pages, url: "${{ steps.deployment.outputs.page_url }}" }
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
        id: deployment
```

In **Settings → Pages**, set the source to **GitHub Actions**.

For a custom domain, add a `CNAME` file to `public/` containing your domain (e.g. `demo.salvium.io`), and add a DNS CNAME record pointing to `<your-github-username>.github.io`.

Update `vite.config.ts` depending on how you deploy:

| Scenario | `base` value |
|----------|-------------|
| Custom domain (`demo.salvium.io`) | `'/'` |
| GitHub Pages subdirectory (`user.github.io/repo-name`) | `'/repo-name/'` |

The demo uses `HashRouter` (`/#/create`, `/#/token/PROP`) so all routes work correctly on static hosting without server-side redirects.
