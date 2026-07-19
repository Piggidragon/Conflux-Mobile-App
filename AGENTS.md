# AGENTS.md

Compact guidance for sessions in the **Conflux Conduit fork** — the mobile Flutter client for [Conflux](https://github.com/Piggidragon/Conflux), forked from [`cogwheel0/conduit`](https://github.com/cogwheel0/conduit) (GPL-3.0). Sits alongside the Conflux Svelte web UI as the second frontend; both consume the same Conflux backend via the Open WebUI REST + Socket.IO + (where applicable) Hermes-direct REST surface.

The Conflux compat contract for this fork is owned by `../Conflux`/`PLAN.md` §3.7 + Guardrail 20. The mobile fork's working assumption is **upstream-compatible-by-default**: keep the Open WebUI REST + Socket.IO contract + upstream Conduit data model intact so rebasing upstream stays tractable.

---

## Status: heavy development, not production ready

> **⚠️ This repository is heavy development and is not production ready in any way.** The
> `main` branch currently mirrors the **unchanged upstream**
> [`cogwheel0/conduit`](https://github.com/cogwheel0/conduit) repository (`v3.4.3+136`),
> rebranded only by README / AGENTS.md reframing and the new `lib/features/conflux_*/`
> directories. **`main` does not carry Conflux code.** All Conflux work lives on `dev`
> and feature branches (currently `chore/conflux-fork-init` and
> `chore/plan-and-cross-refs`); the `main` branch is the **unchanged upstream** and is
> not deployable as a Conflux-built binary without first merging the fork's PRs.
> Do not deploy from `main`; use `dev` (or a pinned feature branch) for any environment,
> and only in contexts where data loss, downtime, and protocol breakage are acceptable.
> The sibling backend repo (`Piggidragon/Conflux`) has the same posture — its `main`
> mirrors unchanged upstream open-webui/open-webui; Conflux work lives on its `dev` +
> feature branches (companion-repos section at the bottom of this file).

---

## Language: English only

Everything in this repository is in **English**:

- Dart source code (identifiers, comments, doc strings, log messages, error strings).
- Documentation (`README.md`, `AGENTS.md`, `PRIVACY_POLICY.md`, `THIRD_PARTY_NOTICES.md`, any other `.md` files).
- ARB localization files (`lib/l10n/*.arb`) — English (`app_en.arb`) is the source of truth; other locales translate from it.
- Commit messages, PR titles, PR descriptions, PR review comments.
- Issue titles, issue descriptions, issue comments.
- Branch names and branch descriptions (`git config branch.<name>.description`).
- Changelog entries, release notes, tags.

Only communication in this chat with the operator may happen in another language. The repository stays English.

---

## GitHub workflow

### Branching

- One branch = one theme. Don't mix unrelated changes (e.g. a docs cleanup and a refactor) in the same branch; split them.
- Branch names follow `<type>/<short-kebab-description>`, optionally with the issue number first: `<type>/<issue#>-<short-description>`. `<type>` is one of the conventional commit prefixes used in this repo: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `perf`, `build`, `ci`, `i18n`, `style`. Keep the description short and concrete (e.g. `feat/honcho-dashboard-entry`, `fix/12-hermes-sse-parser`).
- Add a branch description with `git config branch.<name>.description "..."` — one or two sentences explaining the branch's purpose; English only.

### Commits

- Atomic commits within a branch: a commit either stands on its own or is a logical step toward the branch's goal. Squash noisy fixups before pushing.
- Avoid huge commits that touch many unrelated files; avoid one-line typo commits unless the change truly is that small. Aim for commits that a reviewer can read in one sitting.
- Commit messages follow the conventional-commits style already in this repo (`feat: …`, `fix: …`, `chore: …`, `docs(conduit): …`, optional scope, present tense, English).

### Pull requests

- Always push the branch when its purpose is finished and open a **draft** PR against `dev`. Use the PR template at `.github/pull_request_template.md`; fill in the description and a **Test Cases** section (see below).
- Drafts stay drafts until a human reviews and approves them. **No AI agent — including the one writing the code — ever merges a PR automatically.** Merging requires a human reviewer who has read the diff, run the listed test cases, and confirmed there are no regressions.
- Every PR must list **specific, concrete test cases** the human reviewer should run manually (or via the project's test suite). Generic statements like "tested manually" are not enough. Each test case names the input/condition, the action, and the expected outcome — e.g. `Given the Hermes direct-connection is enabled and the server returns an audio-only response, when the user pastes a 2 kB text into the prompt, then the chat list does not duplicate within five seconds.`

### Target branches

- `dev` is the integration branch. **All feature / bugfix / chore PRs target `dev`.** The PR template enforces this; PRs targeting `main` are closed automatically.
- `main` is reserved for true releases. A release happens by merging `dev` into `main` (typically a fast-forward or a release commit), tagging the result, and bumping the version (see _Versioning_ below). No feature work targets `main` directly.

### Versioning

- **Scheme:** 3-digit Semantic Versioning with an optional pre-release suffix — `MAJOR.MINOR.PATCH[-dev.N | -rc.N]`. PATCH can grow freely; there is no `10.3.1` ceiling.
- **Bump rules:**
  - Per-branch in-development build on `dev` → append or increment `-dev.N` (e.g. `3.4.4-dev.1`, `3.4.4-dev.2`). One `-dev.N` step per branch merged to `dev`.
  - Major feature landing → bump MINOR, reset PATCH to `0`, restart at `-dev.1`.
  - Release candidate → drop the dev suffix, append `-rc.N`.
  - True release → drop the suffix.
- **Source of truth:** `pubspec.yaml` `version:` field. Edit only that file on a release; `scripts/release.sh` automates the bump.

---

## Project

The Conflux Conduit fork is the **mobile frontend** of Conflux (`Piggidragon/Conflux`), alongside the rebranded Open WebUI Svelte web. It connects to the same Conflux backend via REST + Socket.IO + (where applicable) Hermes-direct REST. Compatibility contract lives in the backend repo (`PLAN.md` §3.7 + Guardrail 20); Conduit-side adaptations add Conflux-branded strings, Conflux-specific feature modules (Honcho dashboard entry, profile-switcher surface, three-system memory integration), and Conflux-specific tweaks to upstream Conduit surfaces — all behind a working assumption that upstream Conduit stays a clean rebase target.

For Flutter build / architecture / auth / conventions / gotchas details, the upstream Conduit contract still applies (Riverpod, Drift, Dio, `socket_io_client`, GoRouter stack is unchanged). See the upstream project at [`cogwheel0/conduit`](https://github.com/cogwheel0/conduit) for non-Conflux decisions; this fork does not duplicate that documentation here.

## Build, codegen, and verification

```bash
flutter pub get
dart run build_runner build
flutter run -d ios
# or
flutter run -d android
```

```bash
flutter pub get
dart run build_runner build
flutter test
flutter analyze
```

Run `dart run build_runner build` after `flutter pub get` and after switching branches or creating a fresh worktree. Generated `*.g.dart` and `*.freezed.dart` files are git-ignored but required by analyzer/test runs. `flutter test` and `flutter analyze` are local verification gates before handoff; CI workflows are `.github/workflows/l10n.yml` and `.github/workflows/release.yml`. The release workflow is operator-driven, not per-PR.

## Architecture & layout

Top-level Dart code is split into `lib/core/` for app-wide services, models, routing, auth, storage, networking, and platform glue; `lib/features/` for product areas; `lib/shared/` for reusable widgets and utilities; and `lib/l10n/` for localization ARB files and generated localization output configured by `l10n.yaml`. Do not hand-edit generated localization Dart; edit ARB inputs and regenerate.

State management uses Riverpod 3 with generated providers. Navigation uses `go_router`. HTTP and realtime transport use Dio and `socket_io_client`. Local persistence uses Drift for structured data (chats, notes, caches, outbox), `shared_preferences` for preferences, and `flutter_secure_storage` for credentials; residual Hive CE code is staged for removal.

## Conventions

Use `DebugLogger` from `lib/core/utils/debug_logger.dart` for diagnostics, with slash-scoped `scope:` values such as `auth/proxy`, `streaming/helper`, or `models/default`; do not add raw `print` calls. Tests use `package:checks`, `flutter_test`, and `mocktail`. Lints come from `flutter_lints` and `riverpod_lint`.

## Auth subsystem map

Auth spans `lib/core/auth/`, `lib/features/auth/views/`, and `lib/features/auth/providers/unified_auth_providers.dart`. Start with `lib/features/auth/views/server_connection_page.dart` for server URL setup, custom headers, proxy detection, and reverse-proxy handoff. `lib/features/auth/views/authentication_page.dart` covers username/password, LDAP, manual JWT token entry, and SSO entry points. `lib/features/auth/views/sso_auth_page.dart` handles Open WebUI SSO/OAuth in a WebView, while `lib/features/auth/views/proxy_auth_page.dart` handles reverse-proxy login pages and cookie/JWT capture.

Cookie helpers live in `lib/core/auth/webview_cookie_helper.dart` and `lib/core/auth/native_cookie_manager.dart`. `lib/core/auth/auth_state_manager.dart` owns token restore, login, logout, and secure persistence. `lib/core/auth/api_auth_interceptor.dart` injects bearer tokens and custom headers into configured Dio requests. `lib/core/auth/token_validator.dart` handles JWT/API-key format checks and server validation. Credentials and auth tokens must live in `flutter_secure_storage` through `SecureCredentialStorage`; auth-bearing headers should stay scoped to clients configured for the selected `ServerConfig.url`.

When adding a Conflux-specific auth path (e.g. for the Conflux Conduit fork's Conflux-branded server preset), layer it on top of the existing flow without changing the upstream contract — new code lives in `lib/features/auth/views/conflux_*` and registers through the same `unified_auth_providers`.

## Gotchas

`lib/core/services/api_service.dart` is about 6000 lines and mixes many endpoint families, so verify endpoint names against the upstream API reference before adding or changing API calls. Markdown from chat content is sanitized in `lib/features/chat/views/chat_page.dart`, but Mermaid and ChartJS blocks can render through WebViews in `lib/shared/widgets/markdown/markdown_config.dart`; treat model output as untrusted when changing that pipeline. Fresh worktrees may be missing generated Dart files because they are git-ignored, so run build_runner before assuming analyzer failures are real source errors.

Conflux-fork gotcha: **the compat contract is owned by the backend.** When extending Conduit (e.g. add a Hermes-direct endpoint surface, change a Socket.IO event payload, retire a deprecated Open WebUI route), the matching backend PR in `Piggidragon/Conflux` is part of the same PR cycle — Conduit alone is half a change. See `../Conflux`/`PLAN.md` §3.7 + Guardrail 20 for the contract surface.

---

## Repo layout

| Path                            | Role                                                                |
| ------------------------------- | ------------------------------------------------------------------- |
| `lib/core/`                     | app-wide services, models, routing, auth, storage, networking       |
| `lib/features/`                 | product areas (chat, channels, notes, profiles, prompts, tools, ...) |
| `lib/features/conflux_*`        | Conflux-specific feature modules (memory integration, branding)      |
| `lib/features/hermes/`          | direct Hermes Agent transport (config, sessions, jobs, runs)         |
| `lib/shared/`                   | reusable widgets, theme tokens                                       |
| `lib/l10n/`                     | localized strings (English source of truth, see `l10n.yaml`)         |
| `pigeons/`                      | native bridge definitions (Flutter ↔ Kotlin / Swift)                 |
| `tool/`                         | Dart scripts (locale / ARB validation)                               |
| `scripts/release.sh`            | operator-driven release tooling                                       |
| `assets/icons/`                 | app / server / Hermes icons                                          |
| `ios/`, `android/`              | platform config                                                       |
| `test/`                         | Flutter unit + widget tests                                           |
| `references/upstream/`          | (gitignored) local clone of `cogwheel0/conduit` for diffing          |
| `.github/pull_request_template.md` | draft-PR template (Test Cases section required)                   |

## Cross-repo coordination

- **`Piggidragon/Conflux`** holds the REST + Socket.IO + Hermes-direct compat contract this client depends on (see that repo's `PLAN.md` §3.7 + Guardrail 20). Breaking changes are coordinated across both repos in the same PR cycle; the Conduit side alone is half a change.
- **`cogwheel0/conduit`** is the source of truth for the Flutter codebase. Rebasing the Conflux fork onto upstream keeps the diff tractable; minimise Conflux-specific patches outside `lib/features/conflux_*` and cross-cutting adapter files.
- Rebase cadence is operator-decided. Until the first upstream rebase, this fork's diff against `cogwheel0/conduit@main` is the audit trail.

## Style

- **Dart:** `flutter_lints` + `riverpod_lint` (configured in `analysis_options.yaml`).
- **Generated files** (`*.g.dart`, `*.freezed.dart`): do not hand-edit.
- **Localization:** update `lib/l10n/app_en.arb` first; run the `l10n` workflow (or `flutter gen-l10n`) to regenerate derived files.
