<!-- Draft PR template — keep as draft until human review.

This template mirrors `Piggidragon/Conflux/.github/pull_request_template.md` so
cross-repo PRs can be reviewed with the same rubric. -->

## Summary

<!--
- What's this PR about?
- Which branch/theme is it on?
- One paragraph max. English only.
-->

## Changes

<!--
- Bullet list of user-visible or behavioural changes
- Note any new files, modules, or refactors
- Flag anything the reviewer should look at twice
-->

## Conflux compat surface

<!--
Required by AGENTS.md Guardrail 20 / §3.7 of the backend PLAN.md.

Pick exactly one or `None`:

- [ ] **Breaks the Open WebUI REST API surface** — list affected routes
- [ ] **Breaks the Open WebUI Socket.IO event surface** — list affected events
- [ ] **Breaks the Hermes-direct REST / WebSocket surface** — list affected endpoints
- [ ] **No break** — confirm `None` and link to the Conflux backend PR that lands
      first (if any), so reviewers can verify cross-repo alignment.

If a break is needed, the matching backend PR in `Piggidragon/Conflux` MUST be
linked below before this PR can merge; otherwise the mobile fork drifts from the
backend's compat contract.
-->

Related Conflux backend PR: <!-- link or `None` -->

## Test Cases

<!--
REQUIRED. Specific, concrete test cases the human reviewer runs manually (or via
the project's test suite). "Tested manually" alone is not enough.

Each test case names:
  - the input / pre-condition
  - the action the reviewer takes
  - the expected outcome

Examples:

- Given the Hermes direct-connection is enabled and the server returns an audio-only
  response, when the user pastes a 2 kB text into the prompt, then the chat list does
  not duplicate within five seconds.

- Given a profile with skill X enabled, when the user invokes slash command /x with
  no args, then the chat response cites SKILL.md step 2 within two turns.

Add as many as needed; number them.
-->

1.
2.
3.

## Checklist

<!-- Required gates. PR stays draft until all are checked. -->

- [ ] `flutter pub get` runs clean on a fresh worktree
- [ ] `dart run build_runner build` succeeds without manual edits
- [ ] `flutter test` passes (or failures are explained in the Test Cases section)
- [ ] `flutter analyze` is clean (or remaining warnings are documented)
- [ ] No hand-edits to `*.g.dart` / `*.freezed.dart`; `pigeons/` definitions regenerate the natives
- [ ] `lib/l10n/app_en.arb` updated first; translations regenerated
- [ ] Branch description set with `git config branch.<name>.description`
- [ ] Commits follow conventional-commits style (English, present tense, scoped where useful)
- [ ] PR stays **draft** until a human reviewer signs off

## Notes for the reviewer

<!--
Anything the reviewer should pay attention to: experimental packages, manual
verification steps, follow-up TODOs, links to design docs / Loom / screenshots.
-->
