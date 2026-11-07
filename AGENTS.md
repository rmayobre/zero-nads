# AGENTS.md

Guidance for AI coding agents working in this repository.

## Project

`zero_nads` is a Dart 3.x (SDK `^3.11.0`) functional programming library built on **extension types** — zero-cost wrappers providing `Option`, `Either`, `Result`, `Task`, `Flow`, `Effect`, and `Snapshot`, plus precondition helpers and `Stream`/`Iterable`/`Future` extensions. The only runtime dependency is `meta`.

## Commands

```sh
dart pub get          # install dependencies
dart analyze          # static analysis (package:lints/recommended)
dart format .         # format
dart test             # run all tests in test/
dart test test/option_test.dart   # run a single test file
dart run example/option_example.dart
```

Run `dart analyze` and `dart test` before considering a change done.

## Layout

- `lib/zero_nads.dart` — the single public entry point; only re-exports files from `lib/src/`. Add an `export` here for any new public file. Do **not** export `lib/src/internal/`.
- `lib/src/<type>.dart` — one file per type or extension group (`option.dart`, `either.dart`, `result.dart`, `task.dart`, `flow.dart`, `effect.dart`, `snapshot.dart`, `preconditions.dart`, and extensions in `future.dart`, `stream.dart`, `iterable.dart`).
- `lib/src/inline.dart` — shared inline helpers.
- `lib/src/internal/` — `@internal` annotations/typedefs/constants (`eager`, `lazy`, `throws`, `Calculation`, `Predicate`, `Reducer`, `Transformation`, `Callable`, message constants).
- `test/<name>_test.dart` — one test file per source file, importing `package:zero_nads/zero_nads.dart`.
- `example/` — runnable usage examples, one per type.

## Conventions

- **Zero cost**: types are `extension type`s wrapping a single value. Don't add classes or extra allocations that defeat this.
- **Eager vs. lazy**: annotate extension types with `@eager` (holds a concrete value, e.g. `Option`) or `@lazy` (wraps a deferred computation, e.g. `Task`, `Effect`, `Flow`). Lazy types must not execute anything until explicitly forced (`.run()`, `.collect()`).
- **`@throws`**: annotate any function that can throw (directly or via a callback it invokes) and document what can be thrown in its doc comment.
- Use the shared typedefs from `internal/typedefs.dart` for function parameters rather than inline function types.
- Use preconditions (`check`, `require`, `checkNotNull`, …) from `preconditions.dart` and the message constants in `internal/constants.dart` for guard clauses.
- Public members get `///` doc comments in the existing style (summary, then `@param` / `@return` / `@throws` lines).
- Keep new APIs consistent across sibling types (e.g. `map`, `flatMap`, `getOrDefault`, `getOrElse`, `getOrNull`, `getOrThrow` naming).
- Tests use `package:test` with `group`/`test`, grouped by constructors/behaviour per type. Add tests for every new or changed public member.
- When adding or changing public API, update `README.md`, `CHANGELOG.md`, and the matching file in `example/`.

## Repository notes

- `pubspec.lock` is gitignored (library package).
- Releases are published to pub.dev by `.github/workflows/publish.yml` when a `vX.Y.Z` tag is pushed. The tag must match the `version:` in `pubspec.yaml`.
