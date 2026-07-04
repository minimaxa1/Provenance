# Prov — by Vektor Memory

> Provenance - what your code looked like, and when. Cryptographic and verifiable, works on any codebase.

![Uploading image.png…]()


[!\[License: Apache-2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](./LICENSE) [!\[npm](https://img.shields.io/badge/npx-prov-black)](https://npmjs.com/package/prov)

Prov stamps proprietary headers into your source, snapshots your codebase into a Merkle-tree manifest bound to your git commit, and anchors that manifest with two independent timestamp authorities — RFC 3161 (FreeTSA) and OpenTimestamps (Bitcoin). If you ever need to prove what your code looked like at a point in time, this is the evidence trail.

Part of the Vektor ecosystem alongside [Via](https://github.com/Vektor-Memory/Via) and [Vex](https://github.com/Vektor-Memory/Vex).

\---

## The problem

Copyright headers get stripped. Timestamps from a single authority are a single point of failure. Manifests that hash a flat file list can't prove a single file belongs to a snapshot without exposing every other file in it. And most provenance tooling is hardcoded to whoever wrote it, so nobody else can use it without rewriting it.

Prov solves all four: config-driven, Merkle-provable, dual-anchored, and CI-checkable.

\---

## Install

```
npm install -g prov
# or run without installing
npx prov --help
```

**Requirements:** Node.js >= 18, `openssl` and `curl` on PATH. Optional: `pip install opentimestamps-client` for the second timestamp anchor.

\---

## Commands

### `prov init`

Run it with no flags in a real terminal and it's an interactive wizard — asks owner, product, license ID, and evidence folder, shows you a summary before writing anything, then offers to run `prov stamp add` immediately.

```
prov init
```

```
Provenance setup wizard
Answer a few questions, or press Enter to accept the default shown in parentheses.

? Owner / company name: (Your Company Pty Ltd)
? Product name: (my-project)
? SPDX license identifier: (LicenseRef-myproject-Proprietary)
? Evidence output folder: (.provenance)

Summary:
  owner       Acme Pty Ltd
  product     Acme SDK
  licenseId   LicenseRef-AcmeSDK-Proprietary
  outDir      .provenance

? Write provenance.config.json with these values? (Y/n)
✓ Created provenance.config.json for "Acme SDK".
? Run 'prov stamp add' now to insert headers? (Y/n)
```

For CI or scripted setup, pass flags and it skips the wizard entirely:

```
prov init --owner "Acme Pty Ltd" --product "Acme SDK"
prov init --owner "Acme Pty Ltd" --product "Acme SDK" --license-id "LicenseRef-Acme-Proprietary"
```

### `prov stamp`

Insert proprietary headers into every source file, shebang-safe.

```
prov stamp add
prov stamp check                 # CI: exit 1 if any file is missing a header
prov stamp preview               # dry run, no writes
```

### `prov manifest`

Snapshot the codebase into a Merkle tree, bound to your current git commit.

```
prov manifest create
prov manifest prove src/index.js  # standalone proof this file was in the snapshot
```

### `prov timestamp`

Anchor the manifest with two independent timestamp authorities.

```
prov timestamp create
```

### `prov verify`

Re-check file hashes, the Merkle root, and both timestamps.

```
prov verify
```

### `prov notice`

Write a human-readable AI/LLM use notice and licence notice into the provenance directory.

```
prov notice create
```

### `prov status`

Full provenance health in one command.

```
prov status
# → headers stamped, manifest present, timestamps anchored, bound git commit
```

\---

## Anchors

|Anchor|Trust model|Confirmation time|
|-|-|-|
|RFC 3161 (FreeTSA)|Third-party timestamping authority|Immediate|
|OpenTimestamps|Bitcoin blockchain, no third party|Hours|

Using both means no single timestamping authority staying online or trusted is a dependency for your evidence.

\---

## What to commit vs. ship

* **Commit to git**: `manifest.json`, `manifest.tsr`, `manifest.json.ots` — this is your evidence.
* **Don't commit**: `manifest.tsq`, `cacert.pem` — transient, regenerable. See `.gitignore`.
* **Don't ship in your published package**: `.provenance/`, `provenance.config.json`, `.github/` — customers need your code, not your evidence trail. See `.npmignore`.

\---

## CI

`.github/workflows/provenance-check.yml` runs `prov stamp check` on every PR and push to main, and uploads a manifest artifact tied to each merge commit.

\---

## What this does not do

This is evidence and stated policy, not a technical access-control or anti-piracy mechanism. It won't stop a determined bad actor from copying your code — it gives you strong, verifiable evidence of what your code looked like and when, useful if you ever need to demonstrate prior authorship.

Pair it with a clear commercial licence/EULA and, where feasible, keeping your highest-value logic out of anything you ship in source form.

\---

## Vektor ecosystem

|Tool|What it does|
|-|-|
|[Via](https://github.com/Vektor-Memory/Via)|Route context and execution across all AI tools|
|[Vex](https://github.com/Vektor-Memory/Vex)|Migrate agent memory between vector stores|
|**Prov**|Cryptographic proof-of-authorship for any codebase|
|[Slipstream](https://vektormemory.com)|The intelligence engine underneath — graph memory, vector search, stealth fetch, multimodal|

\---

## License

Apache 2.0 — free forever. Built by [Vektor Memory](https://vektormemory.com).

