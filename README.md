<div align="center">

# Wallet Recovery Console

**Find what your wallet left behind across 36 EVM chains — and recover it in one click.**

Sunucusuz · anahtarsız · tek dosya · MIT

[![Live](https://img.shields.io/badge/live-bornoz.github.io%2Fwallet--recovery--console-e4b04a?style=flat-square)](https://bornoz.github.io/wallet-recovery-console/)
[![License: MIT](https://img.shields.io/badge/license-MIT-2b3040?style=flat-square)](LICENSE)
[![Chains](https://img.shields.io/badge/chains-36-3ddc97?style=flat-square)](#coverage)
[![No backend](https://img.shields.io/badge/backend-none-6ea8fe?style=flat-square)](#security-model)

</div>

---

## What it does

Paste an address. The console scans 36 EVM networks for assets that wallets and portfolio apps do not show:

- **Concentrated-liquidity positions** (Uniswap v3, PancakeSwap v3, Algebra/Kim, Velodrome Slipstream, Zebra, …) — the withdrawable amount is measured by simulating `decreaseLiquidity + collect` on behalf of the owner, not estimated.
- **Classic LP tokens** (Uniswap v2, Solidly-style pairs) — pool share converted to underlying amounts.
- **Lending deposits** — Aave-style aTokens and Compound-style cTokens.
- **Delayed redemptions** — e.g. Bedrock uniBTC on Bitlayer, with the exact unlock time.
- **Unredeemed bridge transfers** — Wormhole operations that never completed on the target chain.
- **Locks and escrows** (ve-, x-, es- tokens) and native balances on every chain.

Everything that is withdrawable gets a **Withdraw** button. The wallet switches chain, shows each transaction, and you approve it. The page cannot sign anything.

**Türkçe:** Adresi yapıştır, **Tara**; unutulmuş likidite, mevduat, kilit ve köprü transferleri üç kutuda toplanır. Çekilebilenlerde **Çek** düğmesi vardır; imzayı yalnız kendi cüzdanın atar. Arayüz TR/EN.

## Live

**https://bornoz.github.io/wallet-recovery-console/** — served by GitHub Pages straight from this repository. Add `?address=0x…` to pre-fill an address.

Running it locally works too (`index.html` is self-contained), but most wallet extensions do not connect to `file://` pages; use an `https` origin for withdrawals.

## Coverage

| Level | Chains | Source |
|---|---|---|
| Full token + NFT index, keyless | Ethereum, Base, Arbitrum, Optimism, Polygon, zkSync Era, Linea, Scroll, Mode, Manta, Soneium, Lisk, Celo, Ink, Metis, Gnosis, Immutable, Unichain, Merlin | Blockscout |
| Full, keyless | Avalanche, Blast, Mantle | Routescan |
| Full, keyless | Bitlayer | BTRScan |
| Full, keyless | Sonic, Fraxtal, Arbitrum Nova, Zora, Abstract, Core, BOB | On-chain `eth_getLogs` (recipient-filtered `Transfer` events, ERC-20 and ERC-721) |
| Partial, keyless | Taiko, Sei, Polygon zkEVM, HyperEVM | Bounded log windows (provider range limits: 2 000 / 2 000 / 10 000 / 900 blocks per query); older history needs an Etherscan key |
| Native balance only | BNB Chain, opBNB (both full with a free Etherscan key), B² Network | — |

An optional **Etherscan API key** (free tier) unlocks full indexes for BNB Chain, opBNB, HyperEVM, Taiko and Sei. The key stays in your browser.

Every one-click action is **simulated on behalf of the owner during the scan** (`eth_call`) and again right before sending. An action whose simulation reverts is listed under *Info* with the decoded reason (for example `Aave 29 · RESERVE_PAUSED · reserve paused`) instead of a button, so no gas is spent on a transaction that cannot succeed.

The coverage matrix on the page shows, per chain, which source answered and whether the read was full, partial or native-only. **An unread chain is never presented as empty.**

## Security model

- No private keys, seed phrases or signatures are ever requested. The page builds unsigned transactions (`to / data / value`) and hands them to the wallet through EIP-1193; the wallet signs.
- No backend, no database, no analytics. Requests go directly from the browser to public RPCs, Blockscout, Routescan, BTRScan, Etherscan, CoinGecko and Wormholescan.
- Prices for tokens come from index exchange rates keyed by contract address, not by symbol — a token merely named "USDC" is not priced.
- LP withdrawals via `transfer → burn` are limited to small positions (≤ $20) because two separate transactions can be front-run; larger positions are listed for router withdrawal.
- The whole application is one readable file. Audit it: [`index.html`](index.html). See [SECURITY.md](SECURITY.md) for reporting.

## Deploy your own

1. Fork the repository.
2. Settings → Pages → Source: **GitHub Actions**. The included workflow (`.github/workflows/pages.yml`) syntax-checks the script and publishes on every push to `main`.
3. Your copy is live at `https://<account>.github.io/wallet-recovery-console/`.

Any static host works as well (Cloudflare Pages, Netlify, Vercel): there is nothing to build.

## Contributing

New chains, new protocol adapters and better source ladders are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). Keep it single-file, keyless-first and signature-free.

## Disclaimer

Not financial advice. Amounts are on-chain reads and simulations at scan time; they can change before execution, and gas is paid by you. A reverted transaction costs gas but loses no funds.

## License

MIT — © 2026 [Bornoz](https://github.com/Bornoz) · [@kansizsavar](https://twitter.com/kansizsavar)
