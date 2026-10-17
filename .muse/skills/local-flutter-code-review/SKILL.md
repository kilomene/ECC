# Flutter and Dart Code Review

## Check project health and run the gates

- Run `flutter analyze` with `muse.exec` and clear every warning before you continue. Zero analyzer warnings is the bar for this review.

- Run `dart format --set-exit-if-changed` with `muse.exec` and format every file it flags.

- Run `flutter test` with `muse.exec` and keep business logic line coverage at 80 percent or higher.

- Read the full diff with `muse.read`. Check `pubspec.yaml` for unused dependencies and unpinned versions.

- Verify `analysis_options.yaml` enables `strict-casts`, `strict-inference`, and `strict-raw-types`.

- Reject any `print()` calls in production code. Replace them with `dart:developer` `log()` or the project logger.

## Fix Dart language pitfalls

- Replace every `!` operator with `?.`, `??`, or Dart 3 pattern matching. The bang operator moves null errors to runtime.

- Treat `late` as a last resort and use nullable fields or constructor initialization instead. `late` hides initialization failures until the field is first read.

- Wrap fire-and-forget futures in explicit `unawaited()` calls. Bare futures leave errors unhandled and silent.

- Build strings iteratively with `StringBuffer`, never with `+` in a loop. Prefer `final` locals over `var` wherever the value never changes.

- Catch specific exception types with `on` clauses instead of bare `catch (e)`. Never catch `Error` subtypes since they signal bugs in the code itself.

- Prefer Dart 3 records for multi-value returns over single-use DTO classes. Prefer switch expressions and `if-case` over `is` checks with manual casts.

## Enforce widget and state rules

- Keep every `build()` method at 80 to 100 lines or fewer. Split widgets by rebuild boundaries so unchanged subtrees stay const.

- Extract private `_build*()` helper methods into real widget classes. Helper methods block element reuse and const propagation.

- Propagate `const` through constructors, literals, and static subtrees. Const widgets stop rebuild propagation through the tree.

- Add `ValueKey` to items in lists and grids so state survives reordering.

- Never use `UniqueKey` inside `build()` since it forces a rebuild every frame.

- Read colors from `Theme.of(context).colorScheme` and text styles from `Theme.of(context).textTheme`. No hardcoded `Colors.red` or raw hex values anywhere.

- Keep network calls, file I/O, subscriptions, and heavy computation out of `build()`. Check `context.mounted` after every await before touching context or navigating.

- Give each state manager one responsibility and split god managers that handle unrelated concerns.

- Model async work with sealed classes or the library async type. Never use boolean flag soup like `isLoading` plus `isError`, since it allows impossible states.

- Handle every state variant exhaustively in the UI. Loading states carry no stale data and error states carry the error info needed for display.

- Cancel subscriptions, close stream controllers, and stop timers in `dispose()` or `close()`. Prefer declarative builders over manual `.listen()` subscriptions.

- Keep collection sorting, filtering, regex compilation, and image decoding out of `build()`. Compute them in the state management layer instead.

- Use `ListView.builder` for long or dynamic lists. Cache network images with placeholders and error widgets.

## Apply the approved patterns

- Guard routes with a GoRouter redirect that checks auth state and reroutes unauthenticated users to `/login`. Reevaluate the redirect with a `refreshListenable` wired to the auth stream.

- Refresh expired tokens with a Dio interceptor that retries a 401 request exactly once. Tag retried requests with an extra flag so the interceptor cannot loop forever.

- Model states as sealed classes when you want compiler-enforced exhaustive switches. Reach for Freezed when you also need union types with `copyWith` and JSON support, and pick one mechanism per feature.

- For large diffs, spawn a parallel `subagent.spawn` review pass while building continues. Fold its findings in, fix them, and re-run `flutter analyze` plus `flutter test` on your own machine before any GitHub push.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
