# Conflux Conduit fork — PLAN

> **Refactored edition companion to [`Piggidragon/Conflux/PLAN-refactored.md`](https://github.com/Piggidragon/Conflux/blob/docs/plan-optimization/PLAN-refactored.md).** This file is the mobile-side roadmap placeholder; the canonical plan-of-record for Conflux (the backend) lives in the backend repo. Cross-references:
>
> - Backend compat contract: backend `PLAN-refactored.md` §3.7 + Guardrail 20
> - Backend cross-repo coordination: backend `AGENTS.md` § Companion repositories
> - Build / architecture / auth / conventions: this repo's `AGENTS.md`
> - Branding + Disclaimer: this repo's `README.md`

## Status: post-main-feature

Mobile feature work begins **after `Piggidragon/Conflux` reaches its first v1 release**. Until then this repo only handles:

- Upstream rebase from [`cogwheel0/conduit`](https://github.com/cogwheel0/conduit).
- Compat-contract alignment when the backend changes a REST path, Socket.IO event, auth flow, Hermes header, or Hermes-direct route (see `PLAN-refactored.md` §3.7 + Guardrail 20 + this repo's `AGENTS.md` § Cross-repo coordination).
- Branding refreshes that follow the same per-deployment licence carve-out as the backend (see below).

Concrete feature tasks appear here once the backend's first release tags. The `chore/conflux-fork-init` PR is the only work merged on the mobile side so far; everything else is forward-looking.

## Conflux rebrand

This fork is a private, non-commercial home-lab adaptation of upstream Conduit. The rebrand follows the same per-deployment licence carve-out the Conflux backend uses (the Open WebUI License's ≤50 end-user / rolling 30-day exception): the operator's deployment of Conflux runs well under that cap, so rebranding the user-visible surface is permitted **for this deployment only**. Anyone forking Conflux-Mobile-App must run their own deployment under their own compliance boundary; downstream deployments exceeding 50 end users in a rolling 30-day window must restore upstream Conduit branding on the mobile side **and** upstream Open WebUI branding on the backend, or obtain commercial licences from the respective rights-holders. The per-deployment caveat is restated in this repo's `README.md` Disclaimer and in `Piggidragon/Conflux/README.md` Disclaimer; further appearances reference those paragraphs rather than restating the licence text.

What the rebrand touches concretely:

- **Brand strings** (`lib/l10n/app_en.arb` and propagated translations) — "Conduit" → "Conflux Conduit" / "Conflux mobile" where appropriate.
- **Server presets** in the auth flow — `lib/features/auth/views/conflux_*` add Conflux-branded default endpoints.
- **Honcho + Understory memory surfaces** in chat (`lib/features/chat/views/conflux_*` hooks) — adapt to the Conflux three-system memory model from `PLAN-refactored.md` §6.
- **Profile switcher surface** in the sidebar — single-tap switch between per-user Conflux profiles (backend `§14`).
- **Favicon / icons** under `assets/icons/` — keep upstream icons as fallbacks; add Conflux icons when the operator provides them.

What the rebrand **does not** touch:

- Upstream Conduit's Flutter data model, route paths, or service class names (rebase-friendly).
- Open WebUI REST + Socket.IO event names + payload schemas (compat contract).
- Hermes-direct REST + WebSocket surface (compat contract).

## Future features (placeholder, post-v1)

Concrete items land here once `Piggidragon/Conflux` is feature-complete. The pool below is a forward sketch only — none of these are committed, scoped, or scheduled; they are placeholders to keep cross-repo conversations anchored. After the backend reaches v1, the operator promotes the relevant items from this pool into discrete `<type>/<short-kebab>` branches with their own Test Cases.

1. **Honcho dashboard entry-point in chat.** Mirror `PLAN-refactored.md` §6 + §22 — a drawer / surface that surfaces the active profile's Honcho theory-of-mind facts alongside the chat stream. Backend-side counterpart: `§6.2 HonchoProvider` + injected-memory transparency panel. Mobile-side: depends on the Honcho REST endpoint set Conflux exposes.
2. **Understory graph overlay.** `PLAN-refactored.md` §22 specifies the WebUI graph placement (O44: still pending). On mobile, a tap-through to a per-profile Understory viewer, rendered natively (force-directed graph is heavy; first cut is a collapsible list with detail pages).
3. **Conflux profile switcher in the sidebar.** One-tap swap between the user's per-Conflux profiles. Backend-side counterpart: `§14.1 Chat–profile binding` + the profile_id on every chat. Mobile-side: surface the active profile's name + colour + emoji in the title bar; profile picker is a native bottom-sheet.
4. **Conflux-branded server preset.** A single-tap "Connect to Conflux" preset that fills the `ServerConnectionPage` with the operator's default Conflux URL + headers (OAuth scope, `X-Hermes-Session-Key`, etc.) — without hard-coding the operator's domain.
5. **Three-system memory transparency panel in chat.** A native version of the Svelte web's "Honcho / Understory" panels (`PLAN-refactored.md` §22) — collapsible cards inside the chat stream showing what each provider injected into the active turn.
6. **Sandbox control surface** (if the backend exposes per-profile sandbox state via the API). Mobile-side: a status pill in the chat header indicating sandbox readiness; tap-through to a sandbox log / pause-resume control. Deferred until the backend has a stable sandbox-state API.
7. **Audio / voice-call mode adapted for Conflux memory hooks.** Upstream Conduit already has a voice-call mode; the Conflux adaption threads Honcho peer-scope + Understory bundle into the call's context. Deferred until the backend's audio path lands.
8. **Kanban board mobile view** (if backend exposes a Kanban REST surface per `PLAN-refactored.md` §12). Native swipeable cards with a Conflux profile scope filter.
9. **Skill bundle editor** — read-only mirror of the Svelte web's skill editor (`PLAN-refactored.md` §8.2). On mobile: list + diff + approve; writes go through the backend's mediated RPC.

The list above is **not** a roadmap; it's a placeholder pool. Cross-repo PRs that surface these features land with their own concrete task lists; the rule is `PLAN-refactored.md` §23 (phase plan) leads, this list follows.

## Cross-repo compat surface

Owned by `Piggidragon/Conflux`:

- Open WebUI REST + Socket.IO endpoints.
- Auth flow (Bearer / cookie / LDAP / SSO / JWT).
- Hermes-direct REST + WebSocket + auth headers (`Authorization: Bearer API_SERVER_KEY`, `X-Hermes-Session-Key`).
- Hermes chat-completion callback (`/api/chat/completed`).
- Hermes session / job / terminal REST routes (`/api/sessions/*`, `/api/jobs/*`, `/api/terminals/*`).

The mobile fork's PR template (`.github/pull_request_template.md`) carries a `Conflux compat surface` section that forces the PR author to declare breaks (or `None`) and link a matching backend PR. The companion `AGENTS.md` § Cross-repo coordination makes the dependency explicit: when the backend ships a contract change, the matching mobile change is part of the same cycle.

## Repo layout

See `AGENTS.md` § Repo layout for the canonical map of `lib/core/`, `lib/features/`, `lib/shared/`, `lib/l10n/`, `pigeons/`, `tool/`, `scripts/`, `assets/icons/`, `ios/`, `android/`, `test/`, and `references/upstream/` (gitignored).

## Style

See `AGENTS.md` § Style for the Dart / generated-files / localisation conventions.

## Reference map

- **Compat contract + Guardrail 20**: `Piggidragon/Conflux/PLAN-refactored.md` §3.7.
- **Backend roadmap and merge scope**: `Piggidragon/Conflux/PLAN.md` (canonical) and `PLAN-refactored.md` (draft refactor — to be merged).
- **Upstream Conduit (Flutter client)**: [`cogwheel0/conduit`](https://github.com/cogwheel0/conduit), licensed **GPL-3.0**; this fork inherits.
- **Open WebUI (server Conduit connects to)**: [`open-webui/open-webui`](https://github.com/open-webui/open-webui), licensed under the Open WebUI License; Conflux rebrands the server-side under the ≤50-user exception.
- **License disclaimer text**: `README.md` Disclaimer + Credits.