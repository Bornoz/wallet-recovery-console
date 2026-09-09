# Security Policy

## Model

- The console never requests, stores or transmits private keys, seed phrases or signatures.
- It prepares unsigned transactions (`to`, `data`, `value`) and hands them to the wallet extension through the standard EIP-1193 `eth_sendTransaction` request. The wallet displays and signs; the page cannot sign.
- All network calls go directly from the browser to public endpoints (chain RPCs, Blockscout, Routescan, BTRScan, Etherscan, CoinGecko, Wormholescan). There is no backend and no analytics.
- The optional Etherscan API key is kept in the browser's `localStorage` and is sent only to `api.etherscan.io`.
- The app is a single static file; it can be audited by reading `index.html`.

## Reporting a vulnerability

Please open a private security advisory on GitHub (Security → Report a vulnerability) or contact the maintainer via the profile links in the README. Do not open public issues for exploitable bugs. Reports are acknowledged within 72 hours.

## Scope notes

- The LP withdrawal path `transfer → burn` is intentionally limited to small positions (≤ $20). Larger positions are listed for manual withdrawal through the router, because two separate transactions can be front-run.
- Findings are derived from public indexes and on-chain simulation; they are not financial advice and may change between scan and execution.
