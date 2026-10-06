# Contributing to ExWDIT

Thank you for your interest in ExWDIT. The project is in early development and follows a phased [roadmap](README.md#roadmap).

## Ground rules

1. **Defensive purpose only.** No malware development, no anti-cheat or security-product bypass features.
2. **Evidence-based language.** Use "Indicator", "Suspicious", "Heuristic", "Requires Investigation". Never claim a heuristic finding proves malicious behavior.
3. **Untrusted input.** Every parser must validate bounds, sizes, and RVAs, and fail with a controlled error instead of crashing.
4. **Evidence integrity.** Never modify the original sample.
5. **Honest code.** No placeholder functions that pretend to work. Clearly mark unimplemented functionality.

## Coding standards

- Modern C++ (C++20), RAII, no raw ownership, strongly typed enums, explicit error handling
- Namespaces, small functions, no giant source files, no global mutable state
- Platform-specific code behind clear interfaces
- UI code is separate from analysis code
- Dear ImGui: stable IDs, `BeginTable` for large datasets, clipping, expensive work outside render functions

## Workflow

1. Open an issue describing the change.
2. Fork, create a feature branch, and keep the project buildable.
3. Add tests (valid, malformed, and truncated inputs for parsers; known vectors for hashes).
4. Open a pull request that references the issue and the roadmap phase.

## Tests

Parsers and analyzers should be tested with valid PE32, valid PE32+, malformed, truncated, invalid section table, invalid RVA, and invalid import directory cases.
