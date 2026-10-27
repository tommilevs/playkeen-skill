---
name: playkeen-app-adapter
description: Use when an LLM must package, port, or adapt an Entware, opkg, Bash, or Keenetic application for the Playkeen catalog and GitHub Release workflow.
---

# Playkeen application adapter

Build a reproducible Playkeen application package from the existing project. Preserve the upstream CLI and service behavior whenever possible; add a thin lifecycle adapter instead of rewriting a working application.

## Required contract

1. The repository name starts with `keenplay-`.
2. The repository root contains `playkeen.yaml`.
3. The package is published through a GitHub Release, never from an arbitrary branch.
4. The Release contains one archive (`.tar.gz`, `.tgz`, or `.zip`) and a `.sha256` checksum asset.
5. The manifest uses `schema: playkeen/v1`, a stable lowercase `id`, semantic `version`, `type` (`bash`, `opkg`, or `hybrid`), and supported `architectures`.
6. Lifecycle paths are relative to the archive root and must not escape it.

## Adaptation decision

- Existing Entware package: use `type: opkg`; delegate install, upgrade, remove, dependencies, init scripts, and autostart to `opkg`.
- Existing POSIX installer: use `type: bash`; map its actions to `install`, `update`, `remove`, and `status` scripts.
- Panel with an Entware package plus a wrapper: use `type: hybrid` and keep the package lifecycle separate from the wrapper lifecycle.
- Do not download and execute an unverified branch script during installation. Pin release assets and verify SHA-256.

## Terminal profile

If the application is interactive, declare:

```yaml
terminal:
  enabled: true
  provider: ttyd
  command: /opt/bin/my-app
  working_dir: /opt/etc/my-app
  port: auto
```

The command must be the existing CLI entry point. Do not replace a terminal program with a second web implementation. Playkeen exposes it only in developer/debug mode and publishes the allocated port through the KeenDNS integration.

## Release checklist

- Run the project’s tests and a dry-run lifecycle test.
- Run `node scripts/package-entware.mjs` when packaging the Playkeen client.
- Build the application archive with executable lifecycle bits preserved.
- Generate `<archive>.sha256` from the exact uploaded bytes.
- Create a GitHub Release and upload both assets.
- Confirm `playkeen.yaml` parses and the registry workflow discovers the repository.
- Test install, update, status, remove, and rollback on the target architecture when available.

## Output expected from an adapting agent

Report the chosen type, lifecycle mapping, supported architectures, Release tag, asset names, checksum verification, and any behavior that remains delegated to upstream. Never claim registry approval until the automated registry workflow has actually indexed the Release.

For the complete manifest examples and S-UI/3X-UI notes, read `references/manifest-examples.md` in this skill.

## Registry discovery

After the Release is published, do not edit the central registry manually. The Playkeen Actions registry searches public repositories whose names contain `keenplay-`, reads the root `playkeen.yaml`, selects a published Release (including prereleases), and records its archive URL and SHA-256. Confirm the result in `https://raw.githubusercontent.com/tommilevs/playkeen-registry/main/registry/index.json`.

## Entware installation target

The reference device is an aarch64 Entware router. Native `opkg` applications should keep their package lifecycle and dependencies in the `.ipk`; Playkeen must not reimplement those steps. Bash adapters must be POSIX `sh`, preserve executable bits, and avoid Bash-only syntax. A Playkeen application can expose both its WEB panel and its terminal CLI: the WEB panel remains its own service, while `terminal` describes the existing CLI for the administrator ttyd bridge.

## Report in English first

Every adapter repository must have an English-first `README.md` and a Russian `README.ru.md`. Report exact Release URLs, checksum assets, registry indexing evidence, and target-device test results. If an upstream installer remains delegated, state the pinned commit and the delegated paths explicitly.
