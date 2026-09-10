<div align="center">

# Wallet Recovery Console

**Find what your wallet left behind across 37 EVM chains, Starknet and Solana, then recover it in one click.**

Sunucusuz · anahtarsız · tek dosya · MIT

[![Live](https://img.shields.io/badge/live-bornoz.github.io%2Fwallet--recovery--console-e4b04a?style=flat-square)](https://bornoz.github.io/wallet-recovery-console/)
[![License: MIT](https://img.shields.io/badge/license-MIT-2b3040?style=flat-square)](LICENSE)
[![Chains](https://img.shields.io/badge/chains-37%20EVM%20%2B%20Starknet%20%2B%20Solana-3ddc97?style=flat-square)](#coverage)
[![No backend](https://img.shields.io/badge/backend-none-6ea8fe?style=flat-square)](#security-model)

</div>

---

## What it does

Paste an address. The console scans 37 EVM networks for assets that wallets and portfolio apps do not show:

- **Concentrated-liquidity positions** (Uniswap v3, PancakeSwap v3, Algebra/Kim, Velodrome Slipstream, Zebra). The withdrawable amount is measured by simulating `decreaseLiquidity + collect` on behalf of the owner, not estimated.
- **Classic LP tokens** (Uniswap v2, Solidly-style pairs): pool share converted to underlying amounts.
- **Lending deposits**: Aave-style aTokens and Compound-style cTokens.
- **Delayed redemptions**: for example Bedrock uniBTC on Bitlayer, with the exact unlock time.
- **Unredeemed bridge transfers**: Wormhole operations that never completed on the target chain.
- **Locks and escrows**: ve-NFT vote locks (unlock when expired), Camelot-style escrowed tokens (claim finished vestings, start a new one), GMX staked GLP. Other escrows link straight to the protocol UI.
- **Starknet**: JediSwap v2 positions and STRK staking delegations (claim rewards, start the exit, complete it after the 7-day window), signed with Braavos or Ready X.
- **Consolidate**: bridge balances from every chain into one asset on one chain (for example ETH on Base) with LI.FI quotes. A gas reserve stays on the source chain, approvals are handled, and every step is simulated first.
- **Token approvals**: contracts that can still spend the wallet's tokens, discovered from `Approval` logs plus a list of known routers, verified with `allowance()`. Unlimited approvals are flagged; revoke is one click (`approve(spender, 0)`, simulated first).
- **Stuck bridge messages**: LayerZero and Stargate messages that are not delivered, and Across deposits that were not filled, each with a link to the page where they can be retried or refunded.
- **Solana**: empty token accounts that still lock rent (about 0.002 SOL each). They are closed in batches of 20 with `closeAccount`, simulated first, signed by Phantom, Solflare or Backpack through the Wallet Standard.
- **Names**: paste `name.eth` or `name.stark` instead of an address. ENS is resolved through the registry on Ethereum, Starknet ID through the naming contract; no third-party API.
- **History and export**: each scan is compared with the previous one for the same address (new and gone findings) and can be exported as CSV or JSON. Nothing leaves the browser.

Everything that is withdrawable gets a **Withdraw** button. The wallet switches chain, shows each transaction, and you approve it. The page cannot sign anything.

**Türkçe:** Adresi yapıştır, **Tara**; unutulmuş likidite, mevduat, kilit ve köprü transferleri üç kutuda toplanır. Çekilebilenlerde **Çek** düğmesi vardır; imzayı yalnız kendi cüzdanın atar. Arayüz TR/EN.

## Live

**https://bornoz.github.io/wallet-recovery-console/** is served by GitHub Pages straight from this repository. Add `?address=0x…` to pre-fill an address.

Running it locally works too (`index.html` is self-contained), but most wallet extensions do not connect to `file://` pages; use an `https` origin for withdrawals.

## Coverage

| Level | Chains | Source |
|---|---|---|
| Full token + NFT index, keyless | Ethereum, Base, Arbitrum, Optimism, Polygon, zkSync Era, Linea, Scroll, Mode, Manta, Soneium, Lisk, Celo, Ink, Metis, Gnosis, Immutable, Unichain, Merlin | Blockscout |
| Full, keyless | Avalanche, Blast, Mantle | Routescan |
| Full, keyless | Bitlayer | BTRScan |
| Full, keyless | Sonic, Fraxtal, Arbitrum Nova, Zora, Abstract, Core, BOB | On-chain `eth_getLogs` (recipient-filtered `Transfer` events, ERC-20 and ERC-721) |
| Full, keyless | Metis, Gnosis (fallback) | On-chain logs via the chain's own RPC |
| Full with the built-in Etherscan key | opBNB, HyperEVM, Taiko, Sei | Etherscan V2 (a shared free key ships with the app; add your own if it hits its limit) |
| Partial, keyless | Polygon zkEVM (10 000-block windows) | Bounded log windows |
| Native balance only | BNB Chain (its token index is behind Etherscan's paid plan), B² Network | none |

When an explorer is down or rate-limits, the ladder continues with on-chain logs. The window sizes were measured per RPC (for example 2 000 blocks on Base, 5 000 on Celo and Immutable, 10 000 on Optimism, Polygon, Linea, Ink and Unichain, 50 000 on Avalanche, 100 000 on Soneium and Lisk, unbounded on Arbitrum, zkSync Era, Mode and Gnosis) and the coverage matrix reports which rung answered.

Solana's public RPCs refuse account enumeration (`getTokenAccountsByOwner`) when the request comes from a browser page, so the Solana card needs your own RPC URL (a free Helius plan is enough). The URL is stored only in your browser. Balance, blockhash, simulation and sending work on the public endpoints.

Prices come from DefiLlama by contract address (batched, no per-IP quota); CoinGecko is only a fallback for native coins. The Etherscan key stays in your browser; the shared key is a free-tier key (5 calls/s, 100 000/day) and can be replaced with your own in the field under the address bar.

Every one-click action is **simulated on behalf of the owner during the scan** (`eth_call`) and again right before sending. An action whose simulation reverts is listed under *Info* with the decoded reason (for example `Aave 29 · RESERVE_PAUSED · reserve paused`) instead of a button, so no gas is spent on a transaction that cannot succeed.

The coverage matrix on the page shows, per chain, which source answered and whether the read was full, partial or native-only. **An unread chain is never presented as empty.**

## Security model

- No private keys, seed phrases or signatures are ever requested. The page builds unsigned transactions (`to / data / value`) and hands them to the wallet through EIP-1193; the wallet signs.
- No backend, no database, no analytics. Requests go directly from the browser to public RPCs, Blockscout, Routescan, BTRScan, Etherscan, DefiLlama, LI.FI, Wormholescan, LayerZero Scan and Across.
- Solana transactions are built without any library (a legacy message with `closeAccount` instructions) and handed to the wallet through the Wallet Standard; the serializer is checked byte for byte against `@solana/web3.js` in development.
- Token prices are looked up by contract address, never by symbol. A token merely named "USDC" is not priced.
- LP withdrawals via `transfer → burn` are limited to small positions (≤ $20) because two separate transactions can be front-run; larger positions are listed for router withdrawal.
- The whole application is one readable file. Audit it: [`index.html`](index.html). See [SECURITY.md](SECURITY.md) for reporting.

## Deploy your own

1. Fork the repository.
2. Settings → Pages → Source: **GitHub Actions**. The included workflow (`.github/workflows/pages.yml`) syntax-checks the script and publishes on every push to `main`.
3. Your copy is live at `https://<account>.github.io/wallet-recovery-console/`.

Any static host works as well (Cloudflare Pages, Netlify, Vercel): there is nothing to build.

## Contributing

New chains, new protocol adapters and better source ladders are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md). Keep it single-file, keyless-first and signature-free.

## Disclaimer

Not financial advice. Amounts are on-chain reads and simulations at scan time; they can change before execution, and gas is paid by you. A reverted transaction costs gas but loses no funds.

## License

MIT · © 2026 [Bornoz](https://github.com/Bornoz) · [@kansizsavar](https://twitter.com/kansizsavar)
