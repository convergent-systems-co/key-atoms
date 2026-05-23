# key-atoms — Goals

> Cryptographic keys as atoms — public keys, key roles, succession chains, revocation records. Catalog #25 of the `*-atoms` ecosystem and foundational for the v1.1.0 trust model.

---

## What this catalog makes civilization-grade

Trust in the `*-atoms` ecosystem is content-addressed and signature-verified. Before v1.1.0, that trust rested on a single Ed25519 root key referenced implicitly across catalogs. The v1.1.0 spec promotes signing material to first-class atoms — versioned, content-addressed, verifiable, rotatable — and consolidates them in a dedicated catalog so that every other catalog can resolve `signer_key_id` references against a shared, authoritative source.

`key-atoms` is the foundation of that trust model. Every atom in every catalog is verified against a key atom that lives here.

## What it catalogs

### Atom types

- **`key`** — Public-key record. Structured `[key]` block: `key_id`, `algorithm`, `public_key` (base64 public material only), `role` (`root` / `editor` / `catalog-maintainer` / `ratifier` / `mirror-operator`), `holder`, `issued_at`, `expires_at`, `succeeded_by`. Self- or root-signed `[[signatures]]`. **Private key material never appears here.**
- **`revocation`** — Key revocation record. References the revoked `key_id`, captures reason and effective timestamp. Root-signed.
- **`succession`** — Rotation chain record linking a retired key to its successor. Signed by both keys where possible (the outgoing key authorizes the handoff; the incoming key acknowledges).

### Compositions: `keychains`

A keychain bundles related keys — for example, the full set of roles held by `convergent-systems-co` for a given issuance period, or the keys designated to operate a particular mirror.

### Quorum rules (declared in `ATOMS.yml`)

- `default` — `1 of role:root` (this catalog is anchored by the root key itself).
- `key` — `1 of role:root` (only root may authorize new key publications during Phase 1).
- `governance/*` — `1 of role:root + 1 of role:ratifier` (governance atoms require multi-party ratification).

## Runtime consumers

- **aish** — Resolves signing keys at runtime when verifying atoms loaded from any catalog.
- **olympus** — Pantheon Modules verify atom provenance before instantiation. Aegis-signing flow uses key-atoms as its key directory.

Every other catalog in the ecosystem is implicitly a consumer for verification purposes; the named consumers are those that resolve key atoms directly at runtime.

## Status & priority

**Current status:** `bootstrap`

**Priority tier:** Tier 0 — Foundational. Every v1.1.0 catalog blocks on this catalog publishing its root key atom.

**Trigger / activation condition:** The ML-DSA-65 root key atom is published by the principal after offline key generation (`scripts/keygen-root-mldsa65.mjs` in the umbrella repository). Until then, this catalog scaffolds the layout and the trust-model contract but holds no atoms.

## Roadmap

### v0.1 — Bootstrap (current)

**Goal:** Catalog scaffolded; v1.1.0 manifest in place; CI green on empty atom set.

**Work:**

- [x] Scaffold repository from agent-atoms template.
- [x] Write v1.1.0 `ATOMS.yml` with `key` / `revocation` / `succession` atom types, ML-DSA-65 required, root quorum rules.
- [x] Reserve namespaces: `atoms/key/`, `atoms/revocation/`, `atoms/succession/`, `keychains/`, `governance/`, `keys/`.
- [ ] Publish the ML-DSA-65 root key atom (principal, follow-up commit).
- [ ] Author the full v1.1.0 atom schema in `schema-atoms` and reference it from this catalog.

### v0.2 — First rotations

**Goal:** First succession chain published; Ed25519 v1.0.0 root atom retired in favor of the ML-DSA-65 root.

**Work:**

- [ ] Publish the legacy Ed25519 v1.0.0 root as a `key` atom with `lifecycle: retired` and `succeeded_by` pointing to the ML-DSA-65 root.
- [ ] Publish the `succession` atom linking them.
- [ ] First per-catalog `catalog-maintainer` key atoms.

### v1.0 — Operational

**Goal:** Every published catalog verifies end-to-end against key atoms held here. Phase 2 governance escalation in effect: multi-party ratification of new root publications, mandatory transparency-log entries.

## Adoption strategy

Adoption is enforced by the spec, not chosen by consumers. Verification against key-atoms is the v1.1.0 requirement; consumers that elect to stay on v1.0.0 may continue to resolve the legacy Ed25519 root through its v1.1.0 retirement record. Phase 2 will tighten this further (no Ed25519 acceptance for v1.1.0+ atoms).

## Civilization-grade property checklist

| Property | Mechanism in this catalog |
|---|---|
| Typed | JSON Schema validates every atom; the structured `[key]` and `[[signatures]]` shapes per spec Part IV |
| Versioned | Every key atom has a semver `version`; rotation creates a new atom rather than mutating in place |
| Machine-readable | `exports/catalog.json` published on every release |
| Composable | `keychains` bundle keys; consumers resolve by `signer_key_id` |
| Open | Dual-licensed Apache-2.0 (code) + CC-BY-4.0 (data) |
| Durable | Quantum-resistant signing (ML-DSA-65 baseline) so signatures produced today remain verifiable indefinitely |

## Related

- **Spec:** [atoms-spec](https://github.com/convergent-systems-co/atoms-spec) — Part IV defines this catalog's atom shapes and trust model
- **Tools:** [atoms-tools](https://github.com/convergent-systems-co/atoms-tools) — validate / export / verify
- **Federation:** [convergent-systems.co](https://convergent-systems.co) — ecosystem federation directory
- **Umbrella:** [atoms](https://github.com/convergent-systems-co/atoms) — every catalog as a git submodule
- **Manifest:** [`ATOMS.yml`](./ATOMS.yml) — this catalog's machine-readable manifest
- **Standard:** [`README.md`](./README.md) — catalog overview and contribution flow
