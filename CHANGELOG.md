# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.4.0] - 2026-07-31

### Changed
- **Breaking:** `OcttoTool.execute` now returns `Promise<ToolResult>` instead of `Promise<string>`. The plugin SDK widened the type to a union of a string and a structured `{ title?, output, metadata?, attachments? }` result. Tool implementations may still return a plain string; code that consumes an execute result must narrow it, for which `outputText` is exported.
- **Breaking:** `@opencode-ai/plugin` moved from 1.0.223 to 1.18.9.

### Added
- Top-level `model` key in `octto.json` overrides the model for every agent at once. Per-agent entries still win where set (#17).
- `outputText` normalizes either `ToolResult` shape to its text.
- End-to-end test harness running octto inside a real opencode session, with a real browser, in a container.

### Fixed
- `allowOther` now renders a freetext choice. The flag was accepted by four schemas and described to the model, but no renderer ever read it, so it was a no-op (#7, #48).
- Question config properties are no longer stripped by Zod on the `start_session` and `push_question` paths, which had left `pick_one`, `pick_many`, `rank` and `rate` with empty option lists (#6, #28).
- Octto no longer leaks its agent objects into the resolved config. A shallow copy let another plugin's in-place mutation reach octto's stored state and persist across resolutions, and let injected prompt fragments stack on every re-instantiation (#51).
- `create_brainstorm` no longer tells the agent to run its own `get_next_answer` loop, which the agent prompt forbids and which records no findings into branch state. An unfinished brainstorm now reports why it stopped (#49).

## [0.1.0] - 2025-01-06

### Added
- Branch-based brainstorming with parallel exploration
- Browser UI for answering questions
- Event-driven answer processing with `await_brainstorm_complete`
- Inline probe logic for automatic follow-up question generation
- User-configurable agent settings via `~/.config/opencode/octto.json`
- Question types: `pick_one`, `pick_many`, `confirm`, `ask_text`, `rank`, `rate`, `slider`, `thumbs`, `show_plan`, `show_options`, `show_diff`, `review_section`
- Dual session system (state persistence + browser WebSocket)
- Design document output to `docs/plans/`

### Security
- Input validation for all question configs (empty options, bounds checking)
- Whitelisted agent config overrides (only model, temperature, maxSteps)
- Session ID path traversal protection
