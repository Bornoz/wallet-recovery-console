# Contributing

Thanks for helping people recover what their wallets left behind.

## Ground rules

- **Single file, no build.** Everything lives in `index.html`. No frameworks, bundlers or runtime dependencies. Google Fonts is the only external asset and it degrades gracefully.
- **Keyless first.** A new data source must work without an API key, or be clearly marked optional (like the Etherscan key).
- **Never sign.** Contributions must not introduce key handling of any kind. Transactions are always handed to the wallet unsigned.
- **Measure, do not assume.** New protocol adapters must derive amounts from simulation (`eth_call` on behalf of the owner) or exact on-chain reads, never from heuristics.
- **Absence is not evidence.** If a source cannot be read, report it in the coverage matrix as `native`/`error`; never present an unread chain as empty.

## Adding a chain

Append a row to `CHAINS` with: key, chain id, name, native symbol, RPC list (2+ public endpoints), explorer URL, CoinGecko id, the source ladder, and the CoinGecko platform id for token prices.

Source ladder values: `bs:<blockscout base>`, `rs` (Routescan), `btr` (BTRScan), `es` (Etherscan V2 with user key), `logs` (full-range `eth_getLogs`), `logs:<window>:<maxWindows>` (bounded scan; mark as partial).

Before opening a PR, verify from a browser origin that each endpoint answers with `Access-Control-Allow-Origin: *`.

## Adding a protocol adapter

Implement detection (how the position is recognised), measurement (how the withdrawable amount is computed) and the action builder (calldata for the wallet). Add the function selector to `SEL` as a 4-byte constant and document the ABI in a comment. Keep each adapter independent so a failing protocol never blocks the rest of the scan.

## Style

Plain ES2020, `'use strict'`, no transpilation. Turkish and English strings live in `I18N`; every user-facing string needs both.

## Checks

```
node -e "const fs=require('fs');const m=/<script>([\s\S]*)<\/script>/.exec(fs.readFileSync('index.html','utf8'));fs.writeFileSync('/tmp/app.js',m[1]);"
node --check /tmp/app.js
```
