# RiskBeacon — riskbeacon-contracts

> Asset and protocol risk attestations with evidence trails.

![RiskBeacon Smart contracts](banner.png)

## About the project

RiskBeacon is an attestation system for crypto-asset and protocol risk, built on Stellar/Soroban. Analysts and auditors publish signed risk attestations about an asset or protocol, each linked to the evidence behind it, so a reader can see not just *what* rating was given but *why*, and who stood behind it. Like a beacon, the latest attestation is easy to find; unlike an opaque score, every claim keeps an auditable evidence trail.

**Who it is for:** Risk analysts and auditors (attesters), and treasuries, integrators and users who consume risk information.

**How the pieces fit together:**

| Repository | Responsibility |
|---|---|
| `riskbeacon-contracts` | On-chain Soroban state and authorization — the source of truth |
| `riskbeacon-backend` | Off-chain indexing, read models and operational APIs |
| `riskbeacon-app` | User-facing web application |

Typical flow:

1. An attester publishes a risk attestation for an asset or protocol on-chain (authorized by the attester's Stellar account).
2. The backend indexes attestations and their evidence references into a queryable read model.
3. A reader opens the web app, browses an asset's attestation history and follows the evidence trail.

## This repository: Smart contracts

The **contracts** repository is the on-chain core of RiskBeacon. It holds the minimum durable state the product needs and enforces who is allowed to change it. Anything that must be trustworthy and publicly verifiable lives here; everything else lives in the app and backend.

### What is included today

- A Soroban smart contract (`RiskBeaconContract`, Rust, `#![no_std]`) in a Cargo workspace (`soroban-sdk` 28).
- `initialize(admin)` — sets the administrator; requires that address to authorize the call and stores it under the `ADMIN` key.
- `record(actor, value)` — writes an `i128` value under the `VALUE` key; requires `actor` to authorize the call.
- `read()` — returns the stored value (defaults to `0` if nothing has been recorded).
- A unit test (`records_value`) that initializes the contract, records `42` and reads it back.
- Size- and safety-focused release profile: `opt-level = "z"`, LTO, `overflow-checks = true`, `panic = "abort"`, stripped symbols.

> The generic `value` model is a placeholder. The architecture notes call for replacing it with a RiskBeacon-specific state machine (see *Roadmap*) before any mainnet deployment.

### Tech stack

Rust · Soroban SDK 28 · Stellar CLI

### Getting started

```bash
# run the contract unit tests
cargo test

# build the WASM contract
stellar contract build

# formatting check (same as CI)
cargo fmt --all -- --check
```

The same three commands are available as `make test`, `make build` and `make fmt`.

## Roadmap

- Replace the generic `i128` value with the RiskBeacon domain model.
- Add granular authorization (admin vs. authorized actors) and emitted events for the backend to index.
- Expand tests to cover unauthorized access and edge cases, then get an independent audit before mainnet.

## Maintainer

`@ollypee22`

## Status

**v0.1.0 development baseline — not audited and not production-ready.**

## Stellar alignment

The project uses Stellar/Soroban where on-chain state is the source of truth or where deterministic settlement is valuable. Off-chain services are kept out of consensus-critical logic.

## License

Apache-2.0
