# Conduit (Conflux Conduit fork)

> **⚠️ Status: heavy development — not production ready.** This repository is heavy
> development and is not production ready in any way. The `main` branch currently mirrors
> the **unchanged upstream** [`cogwheel0/conduit`](https://github.com/cogwheel0/conduit)
> repository (`v3.4.3+136`), rebranded only by README / AGENTS.md reframing and the new
> `lib/features/conflux_*/` directories. **`main` does not carry Conflux code.** All
> Conflux work lives on `dev` and feature branches; do not deploy from `main`. Use `dev`
> (or a pinned feature branch) for any environment, and only in contexts where data loss,
> downtime, and protocol breakage are acceptable. The sibling backend repo
> (`Piggidragon/Conflux`) has the same posture — its `main` mirrors unchanged upstream
> open-webui/open-webui.

The native Flutter client for [Conflux](https://github.com/Piggidragon/Conflux) — a
self-hosted, home-lab rebranded fork of [Open WebUI](https://github.com/open-webui/open-webui)
that adds the Hermes Agent stack, three-system memory (Honcho + Understory + Qdrant),
the LCM context engine, and per-profile isolation. Sits alongside the Conflux Svelte
web UI as the second frontend; both consume the same Conflux backend via the same
REST + Socket.IO + Hermes-direct surface.

This is the **Conflux Conduit fork**, a maintainer-side adaptation of
[`cogwheel0/conduit`](https://github.com/cogwheel0/conduit) (GPL-3.0). It ships under
the Conflux umbrella as the mobile client of Conflux. The two repos evolve together
— the backend owns the compat contract (see `Piggidragon/Conflux/PLAN.md` §3.7 +
Guardrail 20), the mobile fork adapts to it.

Conduit is an independent client and is not affiliated with Open WebUI Inc. or
cogwheel0. See [Credits](#credits) for component attributions and the upstream chain.

---

## Why Conduit

Open WebUI is excellent on the desktop, but mobile usually breaks down at the
edges: authentication, streaming stability, sharing content into a prompt, and
working quickly from the home screen. Conduit is built to close that gap with a
native client that respects self-hosted deployments and still feels polished
enough for daily use.

## Feature Snapshot

| Area | Included |
| --- | --- |
| Chat | Real-time streaming, model selection, temporary chats, conversation search, and folder management |
| Direct connections | Multiple OpenAI-compatible or Ollama profiles, Chat Completions or Responses, LM Studio and Azure-compatible settings, custom headers, model prefixes and tags, manual model IDs, image prompts, reasoning, usage, stop, and regenerate without requiring an Open WebUI backend |
| AI workflows | File and image uploads, re-attaching previously uploaded server files, multimodal prompts, server-side tools, saved prompts with variables, model-specific toggle filters, and optional web search or image generation when supported by your server |
| Authentication | Username and password, LDAP, JWT, custom headers, SSO/OAuth, and reverse proxy login flows |
| Productivity | Notes with autosave, pinning, AI-generated titles, AI enhancement, audio attachments, channels with threads and reactions when enabled by the server, and sharing from other apps |
| Rendering | Syntax-highlighted code, LaTeX, Mermaid, Chart.js, citations, follow-up suggestions, reasoning blocks, tool-call details, and code execution rendering |
| Mobile UX | Voice input, full voice-call mode, home screen widgets, app quick actions, clipboard image paste, haptics, and adaptive Material/Cupertino UI |
| Personalization | Light, dark, and system themes plus a localized interface across 13 supported locales |
| Privacy | Native secure storage, no third-party analytics or ads, and no developer-operated backend relaying your data |
| Terminal | Interactive terminal sessions over WebSocket with a file browser, shown only when your server exposes the terminal integration |

The Conflux Conduit fork adds (on top of upstream Conduit): Conflux-branded server
presets, three-system memory surface entry points (Honcho dashboard, Understory graph
in chat), per-profile session switching, and alignment with the Conflux profile +
memory model from `Piggidragon/Conflux/PLAN.md` §6 + §14. Active Conflux feature work is
documented in this fork's `CHANGELOG.md`.

## Built for Self-Hosted Reality

- Handles direct Open WebUI sign-in as well as OAuth and SSO providers exposed
  by your deployment.
- Works with reverse proxy setups such as `oauth2-proxy`, Authelia,
  Authentik, Pangolin, and Cloudflare Tunnel by capturing the right cookies and
  session state on-device.
- Supports custom headers during connection setup for environments that depend
  on keys like `X-API-Key`, `Authorization`, or organization routing headers.
- Keeps credentials in Keychain or Keystore instead of plain-text local
  storage.
- Uses WebSocket-backed streaming for fast token-by-token responses and better
  long-running chat reliability.
- Surfaces optional server capabilities such as notes, channels, web search,
  and image generation only when your Open WebUI deployment exposes them.
- Can connect directly to OpenAI-compatible APIs — including LM Studio —
  and native Ollama endpoints. OpenAI-family profiles can use Chat Completions or
  Responses, Bearer or API-key headers, and an optional Azure API version.
  Completion traffic travels from your device to that provider. New chat
  history can use Open WebUI when one is signed in, or remain on this device.

## Quickstart

If you just want to use the **upstream Conduit** as a regular user, install it from the
App Store or Google Play, then follow the upstream
[`cogwheel0/conduit`](https://github.com/cogwheel0/conduit) README. The Conflux
Conduit fork is the same codebase adapted for Conflux branding; install
instructions are unchanged.

To build the **Conflux Conduit fork** from source (for example to test against a
Conflux backend), jump to [Build from Source](#build-from-source).

## Build from Source

### Requirements

- A recent Flutter SDK with Dart `3.9` or newer
- Java 17 for Android builds
- Android 7.0+ (API 24) or iOS 16.0+
- A Conflux backend (`Piggidragon/Conflux`) running, **or** a vanilla Open WebUI
  server (upstream Conduit talks to either transparently), **or** an OpenAI-compatible
  API / Ollama endpoint for direct connections
- Xcode for iOS builds or Android Studio / Android SDK for Android builds

### Run locally

```bash
git clone https://github.com/Piggidragon/Conflux-Mobile-App.git
cd Conflux-Mobile-App
flutter pub get
dart run build_runner build
flutter run -d ios
# or
flutter run -d android
```

### Developer checks

```bash
flutter pub get
dart run build_runner build
flutter test
flutter analyze
```

### Release builds

```bash
# Android
flutter build apk --release
flutter build appbundle --release

# iOS
flutter build ios --release
```

## Localization

Conduit currently ships localized UI strings for English, German, Spanish,
French, Italian, Japanese, Korean, Dutch, Russian, Simplified Chinese,
Traditional Chinese, Czech, and Slovak.

## Architecture

Conduit is a Flutter application organized around feature modules and shared
platform services. It uses Riverpod 3 with code generation for state management
and GoRouter for navigation, with persistent local storage and secure
credential handling built into the core layer.

### Stack

- Flutter for the UI layer
- Riverpod 3 and `riverpod_generator` for state and dependency wiring
- GoRouter for navigation
- Dio plus socket transport for API and streaming
- Drift (SQLite) for structured local data, with shared preferences for settings
- Flutter Secure Storage for credentials

### Project layout

```text
lib/
  core/         auth, routing, models, persistence, platform services
  features/
    auth/       server setup, login, SSO, and proxy auth
    chat/       conversations, attachments, tools, streaming, voice call
    channels/   channel browsing and messaging
    navigation/ chat shell, drawer, and responsive navigation
    notes/      note editor and AI-assisted note workflows
    profile/    theme, preferences, and app customization
    prompts/    prompt helpers and prompt variable UI
    tools/      tool integration surfaces
    hermes/     direct Hermes Agent transport (config, sessions, jobs, runs)
    conflux_*/  Conflux-specific features (memory surfaces, branded presets)
  shared/       reusable widgets, theme tokens, and task infrastructure
```

<details>
<summary>Platform permissions</summary>

- Android requests microphone, camera, and optional location permissions for
  voice input, image capture, and location sharing; attachments use the system
  photo picker, so no broad storage permission is needed.
- iOS requests microphone, speech recognition, camera, photo library, and
  optional location-when-in-use access for voice, attachment, and location
  sharing workflows.

</details>

<details>
<summary>Troubleshooting</summary>

- If streaming stalls, verify WebSocket support is enabled on your Open WebUI
  deployment. The upstream guidance requires
  `ENABLE_WEBSOCKET_SUPPORT="true"`.
- If iOS device builds fail, run `cd ios && pod install` and confirm signing is
  configured in Xcode.
- If Android builds fail, confirm your Java and Gradle toolchain, then try
  `flutter clean`.
- If code generation fails, rerun `dart run build_runner build`.

</details>

## Security and Privacy

- Preferences stay on-device and credentials use platform secure storage.
- Conduit does not include third-party analytics or advertising SDKs.
- Diagnostic logging is local and transient, and Conduit does not relay your
  data through developer-operated backend infrastructure.
- Additional details are documented in [PRIVACY_POLICY.md](PRIVACY_POLICY.md).

## Credits

This fork is derived from the following upstream projects, in order:

- **`cogwheel0/conduit`** — the original Flutter Open WebUI client this fork is built on.
  Copyright (c) 2024-2026 cogwheel0 and Conduit contributors, **GPL-3.0**.
  See [`LICENSE`](LICENSE) (verbatim) and the [upstream repository](https://github.com/cogwheel0/conduit).
- **`open-webui/open-webui`** — the upstream server Conduit connects to.
  Copyright (c) 2023- Open WebUI Inc., **Open WebUI License**.
- **`Piggidragon/Conflux`** — the Conflux server this fork is built for. The Conflux
  rebrand is permitted under the Open WebUI License's ≤50 end-user / rolling 30-day
  exception because the operator's deployment runs well under that cap. The upstream
  `LICENSE` / `LICENSE_NOTICE` / `LICENSE_HISTORY` files in the Conflux repo are
  kept verbatim and unmodified.
- **Third-party notices:** see [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md)
  for asset attributions (Conduit + Conflux icons are derived from upstream sources).

This fork adds Conflux-specific code at `lib/features/conflux_*/` and rebrands the
visible copy in `README.md` / `lib/l10n/app_en.arb` (and propagated translations).
No upstream Conduit source is renamed or refactored except where necessary for the
Conflux compat contract (the ruleset in `Piggidragon/Conflux/PLAN.md` §3.7).

## Disclaimer

This software is provided "as is", without warranty of any kind, express or
implied, including but not limited to the warranties of merchantability, fitness
for a particular purpose, and noninfringement. See [`LICENSE`](LICENSE) for the
full GPL-3.0 text and additional disclaimer language.

The Conflux Conduit fork is a private, non-commercial home-lab adaptation. It is
not affiliated with Open WebUI Inc., cogwheel0, or any commercial Open WebUI
licencee. The Conflux backend that this fork connects to is itself a
non-commercial rebranded fork; see `Piggidragon/Conflux/README.md` (Disclaimer
section) for the deployment-bound licence carve-out that lets the operator
rebrand under the Open WebUI License's ≤50-user exception. **If you fork Conflux
or this Conduit fork and your deployment exceeds 50 end users over a rolling
30-day window, you must restore Open WebUI's upstream branding on both repos
(or obtain a commercial licence from Open WebUI Inc.) — the ≤50-user exception
is per-deployment, not per-fork.**

## Contributing

This fork is maintained privately. Bug reports and discussion live in the Conflux
backend repo's issues / discussions until the operator opens up dedicated
channels here. The upstream Conduit roadmap lives at
[`cogwheel0/conduit/issues`](https://github.com/cogwheel0/conduit/issues)
and [`cogwheel0/conduit/discussions`](https://github.com/cogwheel0/conduit/discussions).

When the operator opens PRs against `dev`, the PR template
(`.github/pull_request_template.md`) requires a **Test Cases** section listing
specific, concrete manual test cases — the same convention used in
`Piggidragon/Conflux`. See this fork's `AGENTS.md` § GitHub workflow for the
branch / commit / PR rules; they mirror the backend repo's so cross-repo PRs can
be reviewed in the same cadence.

## License

This fork is released under the **GPL-3.0 License**, inherited verbatim from
upstream Conduit. See [`LICENSE`](LICENSE) for the full text and
[Credits](#credits) for the source chain.

Conduit is an independent client and is not affiliated with Open WebUI.
