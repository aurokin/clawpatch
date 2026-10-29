# Fork decisions

Notes kept in this fork (aurokin/clawpatch) about provider work. They are not part of
upstream clawpatch and are kept outside `docs/` so they do not appear on the upstream
site. Former tracker keys are given in parentheses as history only.

## Hold the Claude Agent SDK provider (AUR-346, 2026-05-19)

**Decision:** do not add a provider built on `@anthropic-ai/claude-agent-sdk`. Claude
automation goes through the `claude` provider, which shells out to `claude -p`.

**Why:** the SDK would add a new runtime dependency and duplicate most of what the CLI
provider already does, with no shown user value over `claude -p`.

**Reopen only when** maintainers review and explicitly accept the dependency impact,
install and runtime behavior, permission model, and clear user value over `claude -p`.
A reopen also needs official, dated sources for the dependency, license, install size,
runtime behavior, permission model, tool restrictions, auth/config handling, and lazy
loading, plus a recorded maintainer decision accepting the SDK dependency.

## Other provider findings

- **Claude CLI provider (AUR-345):** verified with Claude Code 2.1.144 in isolated
  `--bare` mode with API-key-only auth, `--tools Read,Grep,Glob`,
  `--permission-mode dontAsk`, `--json-schema`, and an empty strict MCP config; an
  adversarial run with hostile project and user hooks, MCP servers, and instructions
  produced no writes, shell, hook, or MCP side effects. Map clawpatch effort `none` to
  omitting `--effort`. Block versions affected by CVE-2026-33068 (< 2.1.53) and
  CVE-2026-40068 (2.1.63 to < 2.1.84).
- **Cursor SDK local provider (AUR-348): rejected.** `@cursor/sdk` 1.0.13 has no
  read-only or tool-restriction control (its sandbox option is on/off only and failed
  to enable locally), and a live smoke showed the parent process environment visible to
  the agent. It is also ToS-licensed and pulls in native helpers and `sqlite3`.
- **Gemini CLI provider (AUR-349):** verified shape
  `gemini --skip-trust -p "" --approval-mode=plan --output-format=json --extensions none`
  with the prompt on stdin; plan mode refused an adversarial edit request. Use plan mode
  for map/review/revalidate and `auto_edit` for fix, never yolo; gate `--skip-trust`
  behind an explicit opt-in; forward only an explicit env allowlist. Minimum version
  0.39.1 (GHSA-wpqr-6v78-jr5g).
- **Antigravity CLI provider (AUR-358): not viable** as of `agy` 1.0.0: no JSON output
  mode, `--print` will not take the prompt on stdin, an auth timeout exits 0, there is no
  `--model` flag, and startup writes a repo-visible `.antigravitycli/<uuid>.json` with
  `allowWrite: true`. Revisit only with proof of reliable JSON-only output, a clean
  target worktree, controllable read-only and write modes, target access through
  `--add-dir`, sane timeout behavior, and a model-selection path.
