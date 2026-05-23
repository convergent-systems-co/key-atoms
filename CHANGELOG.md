# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Bootstrap scaffold for `key-atoms` — catalog #25 in the `*-atoms`
  ecosystem and foundational for the v1.1.0 trust model.
- `ATOMS.yml` conformant to `atoms-spec/v1.1.0`. Atom types: `key`,
  `revocation`, `succession`. Composition type: `keychains`. Required
  signing algorithm: `ml-dsa-65` (accepted: `ml-dsa-65`, `ml-dsa-87`,
  `ed25519` for v1.0.0 backward compatibility). Quorum rules:
  `default` and `key` require `1 of role:root`; `governance/*` requires
  `1 of role:root + 1 of role:ratifier`.
- Reserved namespaces: `atoms/key/`, `atoms/revocation/`,
  `atoms/succession/`, `keychains/`, `governance/`, `keys/`.
- Placeholder atom schema `schemas/atom-v1.1.json` validating the
  identity envelope only; the structured `[key]` block and
  `[[signatures]]` array schemas are deferred to `schema-atoms`.
- Dual licensing: Apache-2.0 (code) + CC-BY-4.0 (data).

### Deferred (handled by the principal in follow-up work)

- ML-DSA-65 root key atom (the first content this catalog will hold).
  Generated offline; private material held only in 1Password. See
  `scripts/keygen-root-mldsa65.mjs` in the umbrella repository.
- Full v1.1.0 structured schema for `[key]` and `[[signatures]]`,
  authored in `schema-atoms`.
- Cloudflare Pages project creation (Terraform apply requires the
  account + dns token pair from the principal's 1Password vault).
