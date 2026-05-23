# key-atoms

> Cryptographic keys as atoms — public keys, key roles, succession chains, revocation records. Catalog #25 of the `*-atoms` ecosystem and foundational for the v1.1.0 trust model.

`key-atoms` is a `*-Atoms` catalog in the [Convergent Systems](https://convergent-systems.co) ecosystem. Under [atoms-spec v1.1.0](https://github.com/convergent-systems-co/atoms-spec), every other catalog depends on this one for signature verification: keys themselves are atoms, and the ML-DSA-65 root signing key that anchors trust for the entire federation is published here.

## Trust model — read this first

`key-atoms` is **catalog #25** in the `*-atoms` ecosystem and is **foundational for atoms-spec/v1.1.0**. Per the spec's Part IV ("Signing and trust"):

- Public keys are published as `key` atoms with the structured shape `id = "key-atoms/key/<key-id>"`, an `[key]` block carrying `key_id` / `algorithm` / `public_key` / `role` / `holder` / `issued_at` / `expires_at` / `succeeded_by`, and self- or root-signed `[[signatures]]` entries.
- Rotation publishes a `succession` atom and updates `succeeded_by` on the rotated key.
- Revocation publishes a `revocation` atom referencing the key being revoked.
- Verifiers walking a key chain follow `succeeded_by` to reach the current authoritative key for a role.

**Private key material NEVER lives in this repository.** Only public-key atoms are published here. Private keys for the convergent-systems-co root role are held offline by the principal in 1Password (vault: `Convergent Systems LLC`). Anyone with commit access to this repo has zero ability to sign on behalf of the federation; signing happens out-of-band, and the signature is published here as the `signature` field of a `[[signatures]]` entry.

**Bootstrap status.** This catalog is in `lifecycle: bootstrap`. The ML-DSA-65 root key atom — the first content this catalog will hold — has not yet been published; it is added by the principal after offline key generation per `scripts/keygen-root-mldsa65.mjs` in the umbrella `atoms` repository. Until that atom lands, no other v1.1.0 atom in the federation can be verified end-to-end. Treat this catalog accordingly.

For the full trust model — supported algorithms (`ml-dsa-65` baseline, `ml-dsa-87` for highest-stakes, `ed25519` accepted for `spec_version <= 1.0.0` backward compatibility only), signer roles (`root`, `editor`, `catalog-maintainer`, `ratifier`, `mirror-operator`), quorum semantics, and the Phase 2 governance escalation that applies specifically to this catalog (multi-party ratification, mandatory transparency-log entries) — see **Atom Spec v1.1.0, Part IV**.

## Structure

```
key-atoms/
├── ATOMS.yml              # Catalog manifest (atoms-spec/v1.1.0)
├── atoms/                 # Public-key atoms by type
│   ├── key/               # Public-key records (no private material)
│   ├── revocation/        # Key revocation records
│   └── succession/        # Key rotation chain records
├── keychains/             # Compositions: named bundles of keys
├── governance/            # Governance atoms (ratifications, role assignments)
├── keys/                  # Reserved per-spec for catalog-scoped keys
├── schemas/               # Catalog-specific JSON Schemas
├── exports/               # CI-generated machine-readable exports
└── docs/                  # Human-readable documentation
```

### Atom types

- `key` — Public-key record. Carries `key_id`, `algorithm`, `public_key`, `role`, `holder`, `issued_at`, `expires_at`, `succeeded_by`. Self- or root-signed. Private key material is never included.
- `revocation` — Revocation record bound to a specific `key_id`, with reason and effective timestamp. Root-signed.
- `succession` — Rotation chain record linking a retired key to its successor. Root-signed by both the outgoing and incoming keys where possible.

### Compositions

- `keychains/` — named bundles of related keys (e.g. all roles held by `convergent-systems-co` for a given period, mirror-operator keychains).

## How to consume

Machine-readable exports are published in [`exports/`](./exports/) on every release:

- `exports/manifest.json` — lightweight discovery (name, version, atom counts).
- `exports/catalog.json` — full catalog dump.

Exports are deterministic and signed under the v1.1.0 structured-signature model. See [`ATOMS.yml`](./ATOMS.yml) for the manifest and the conformance spec.

## How to contribute

Contribution to `key-atoms` is **not** a routine catalog contribution. Publishing or rotating a key affects the verifiability of every atom in every catalog that depends on it. Contribution is restricted to:

1. The catalog steward (`convergent-systems-co`) for routine publication of new keys.
2. Designated ratifiers, per the `governance/*` quorum rule in [`ATOMS.yml`](./ATOMS.yml).

Process for routine work:

1. Read [`ATOMS.yml`](./ATOMS.yml) and Atom Spec v1.1.0 Part IV in full.
2. Generate the key offline. Private key material does not enter this repository, your shell history, your clipboard buffer once verified, or any file under version control.
3. Construct the `key` atom (public material + structured `[[signatures]]`) per the Part IV schema. Sign with the appropriate role's private key offline.
4. Open a PR. CI validates structure, signatures, and references. Manual review by another federation officer is required before merge.

Structural changes to the catalog (new atom types, schema revisions, quorum-rule changes) go through the atoms-spec process — see the [atoms-spec](https://github.com/convergent-systems-co/atoms-spec) repository.

## Licensing

Dual-licensed by artifact class, per the `*-atoms` ecosystem standard:

- **Source code** (`scripts/`, build configuration, web/, validators, Makefile, CI workflows) — Apache-2.0. See [`LICENSE`](./LICENSE).
- **Atom data and documentation** (`atoms/`, `keychains/`, `schemas/`, `exports/`, `docs/`, `*.md`, atom-data `*.json` / `*.toml` / `*.yaml`) — CC-BY-4.0. See [`LICENSE-data`](./LICENSE-data).

See [`NOTICE`](./NOTICE) for the rationale.

## Ecosystem

- **Federation:** [convergent-systems.co](https://convergent-systems.co)
- **Spec:** [github.com/convergent-systems-co/atoms-spec](https://github.com/convergent-systems-co/atoms-spec) — read Part IV.
- **Tools:** [github.com/convergent-systems-co/atoms-tools](https://github.com/convergent-systems-co/atoms-tools)
- **Umbrella:** [github.com/convergent-systems-co/atoms](https://github.com/convergent-systems-co/atoms) — all catalogs as submodules.
- **Sibling catalogs (selection):** agent-atoms, brand-atoms, channel-atoms, compliance-atoms, event-atoms, identity-atoms, knowledge-atoms, model-atoms, persona-atoms, pipeline-atoms, plugin-atoms, policy-atoms, profile-atoms, prompt-atoms, schema-atoms, service-atoms, skill-atoms, theme-atoms, workflow-atoms.
