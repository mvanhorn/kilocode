# Plan: PR #6831 Fixes — Docs Version Switcher

## 1. PR Comment Review & Proposed Responses

### Category A: Config file paths/names are wrong (3 comments, 1 fix)

**Comments:**

- [r2909559737] bot: "`kilo.json` as the recommended file contradicts the actual default path"
- [r2938161681] bot: "supported CLI config filenames table drops JSONC and repeats `kilo.json`"
- [r2938993847] @marius-kilocode: "Yes isn't it `~/.config/kilo/opencode.json`? This is incorrect."
- [r2956230682] @lambertjosh: asks about `.jsonc` versions and `~/.config/opencode/`

**Research findings:**

- The CLI does **NOT** read from `~/.config/opencode/`. It only reads from `~/.config/kilo/`.
- The recommended/default file is **`kilo.jsonc`** (first candidate in `globalConfigFile()` — supports comments).
- Global config files supported (in `~/.config/kilo/`): `config.json`, `kilo.json`, `kilo.jsonc`, `opencode.json`, `opencode.jsonc`
- Project config files supported (root or `.kilo/`): `kilo.jsonc`, `kilo.json`, `opencode.jsonc`, `opencode.json`

**Proposed response:** "Confirmed — the CLI reads from `~/.config/kilo/`, NOT `~/.config/opencode/`. The default file is `kilo.jsonc` (JSONC enables comments). Fixing the table to list all supported filenames."

**Fix:** Replace the config table in `using-in-kilo-code.md:40-43` with:

```
| Scope       | Recommended Path                       | Also supported                                                    |
| ----------- | -------------------------------------- | ----------------------------------------------------------------- |
| **Global**  | `~/.config/kilo/kilo.jsonc`            | `kilo.json`, `opencode.json`, `opencode.jsonc`, `config.json`     |
| **Project** | `./kilo.jsonc` or `./.kilo/kilo.jsonc` | `kilo.json`, `opencode.jsonc`, `opencode.json`                    |
```

---

### Category B: Broken anchor link (1 comment, 1 fix)

**Comment:** [r2909559738] bot: "Renaming heading breaks anchor — `server-transports.md` links to `#understanding-transport-types`"

**Research:** `server-transports.md:199` links to `using-in-kilo-code#understanding-transport-types`. The heading was renamed to `## Transport Types` (anchor becomes `#transport-types`).

**Proposed response:** "Fixing the link in `server-transports.md` to point to `#transport-types`."

**Fix:** Update `server-transports.md:199` to use `using-in-kilo-code#transport-types`.

---

### Category C: KiloClaw nav links use redirect paths (1 comment, defer)

**Comment:** [r2938161665] bot: "KiloClaw hrefs at `/automate/kiloclaw/*` will not show as active in SideNav"

**Research:** The nav links in `automate.ts` use `/automate/kiloclaw/*` paths. Actual pages live under `/kiloclaw/*`. This causes SideNav active-state mismatches.

**Proposed response:** "This is a pre-existing issue unrelated to the version-switcher changes. Filing a separate issue to fix KiloClaw nav paths."

**Action:** Skip in this PR — it's out of scope. Note it for a follow-up.

---

### Category D: Agent Manager inaccuracies (8 comments from @marius-kilocode)

These all relate to `agent-manager.md` and fall into sub-categories:

#### D1: "Parallel mode does not exist anymore" — PARTIALLY WRONG

**Comments:** [r2939006176] and [r2939045250]

**Research:** Parallel mode **does** still exist but is now called "multi-version mode." It creates 1-4 worktrees in parallel, optionally with different models ("Compare Models"). The i18n strings say "worktrees will run in parallel."

**Proposed response:** "The feature still exists as 'multi-version mode' (1-4 worktrees in parallel, optional model comparison). Updating docs to use the current terminology."

**Fix:**

- Line 13: Remove "Parallel Mode" from shared capabilities list
- Lines 95-159: Rewrite the "Parallel Mode and Worktrees" section for the "Pre-release VSCode" tab:
  - Worktrees are the core concept (no "parallel mode" toggle)
  - Users create worktrees from the New Worktree dialog
  - Multi-version mode: create 1-4 worktrees with same prompt (optionally different models)
  - Worktree path: `.kilo/worktrees/`
  - There is also a "local" card representing the user's actual workspace

#### D2: "No automated cleanup" — CORRECT

**Comment:** [r2939041676]

**Research:** Code at `AgentManagerProvider.ts:183-184` explicitly says "Do not auto-remove stale worktrees on load." Users remove worktrees manually.

**Fix:** Lines 110-113 and 145-149: Change "cleaned up automatically" to "must be removed manually by the user (trash icon with confirmation)."

#### D3: "Agent manager manages only worktrees" — PARTIALLY CORRECT

**Comment:** [r2939030966]

**Research:** The Agent Manager has two modes: `worktree` and `local`. The LOCAL card represents the user's workspace. Sessions can exist without worktrees (local sessions). Users can also "promote" a session to create a worktree for it.

**Fix:** Update line 97 and surrounding content to reflect: worktrees + local card architecture, session promotion flow.

#### D4: "CLI prerequisite not needed" — CORRECT

**Comment:** [r2939011995] + lambertjosh's empty suggestion [r2956239960]

**Research:** The pre-release extension bundles the CLI binary — users don't need to install it separately.

**Fix:** Remove line 29 ("Install/update the Kilo Code CLI") from the "Pre-release VSCode" prerequisites tab.

#### D5: "Code review is built in" — ALREADY DOCUMENTED

**Comment:** [r2939056196]

**Research:** Lines 161-180 already document the built-in diff panel and code review. The comment seems to confirm the docs are correct but notes that the button-based flow should be mentioned.

**Fix:** Minor — add that users click the "Review" button to open a tab with changes where they can leave inline comments.

#### D6: "Shortcuts are incomplete" — CORRECT

**Comment:** [r2939058998]

**Research:** The shortcut table at lines 201-206 only lists 3 shortcuts. The actual list from `package.json` has 15+ shortcuts. There's a shortcut helper dialog (`Cmd+Shift+/`).

**Fix:** Expand the shortcuts table to include all shortcuts from the keybinding map, or at minimum reference the shortcut helper: "Press `Cmd+Shift+/` to see all shortcuts."

#### D7: "Remove worktree path `.kilocode/`" — CORRECT

**Comment:** [r2939063815] — suggestion to delete the `.kilocode/worktrees/` path reference.

**Research:** The new extension uses `.kilo/worktrees/`, not `.kilocode/worktrees/`. The `.kilocode/` reference at line 124-135 is wrong.

**Fix:** Change `.kilocode/worktrees/` to `.kilo/worktrees/` throughout.

---

### Category E: VersionContext fallback (1 comment, defer)

**Comment:** [r2909147050] bot (outdated): "Version selection doesn't fall back to current page's platform"

**Proposed response:** "This comment is marked outdated — the VersionContext was reworked since this review."

**Action:** Skip — marked outdated.

---

### Category F: Other bot observations (from summary comment)

- Stale links to `/docs/automate/mcp/using-in-cli` in `automate/index.md:39` and `cli.md:171` — **fix these links to point to `/docs/automate/mcp/using-in-kilo-code`**
- ChatGPT Plus/Pro guidance conflict — **defer**, not related to version switcher

---

### Category G: Resolved comments (already handled)

- [r2938161670] Typo "Demostrating" — resolved
- [r2939004550] Worktree `.git/info/exclude` wording — resolved
- [r2939014377] "git enabled project" — resolved
- [r2939019884] and [r2939022256] — resolved
- [r2939064782] — resolved

---

## 2. Config Directory Research Answer

**Does the CLI read from `~/.config/opencode/`?** **No.**

The app name is hardcoded to `"kilo"` in `packages/opencode/src/global/index.ts:7`. The base config path is always `$XDG_CONFIG_HOME/kilo` (defaults to `~/.config/kilo/`).

The CLI does read filenames with "opencode" in them (`opencode.json`, `opencode.jsonc`) for upstream compatibility, but these files must be inside `~/.config/kilo/`, not `~/.config/opencode/`.

It also searches for `.opencode/` subdirectories in the project tree and home directory (alongside `.kilo/` and `.kilocode/`), reading `opencode.json`/`opencode.jsonc` from within them.

---

## 3. Rename "Classic" / "New Extension" to "VSCode" / "VSCode (Pre-release)"

### Tab label mapping

| Current               | New                          | Notes                                                                 |
| --------------------- | ---------------------------- | --------------------------------------------------------------------- |
| `Classic Extension`   | `VSCode`                     | Current GA release extension                                          |
| `New Extension`       | `VSCode (Pre-release)`       | Pre-release extension on marketplace (standalone tab, extension-only) |
| `New Extension & CLI` | `VSCode (Pre-release) & CLI` | Compound tab — shared behavior/config (31 instances across 13 files)  |
| `New CLI & Extension` | `VSCode (Pre-release) & CLI` | Normalize to same compound format                                     |
| `CLI`                 | `CLI`                        | No change — the CLI is stable, not pre-release                        |

### At GA, the mapping becomes

| Pre-GA                       | Post-GA         |
| ---------------------------- | --------------- |
| `VSCode`                     | `Legacy VSCode` |
| `VSCode (Pre-release)`       | `VSCode`        |
| `VSCode (Pre-release) & CLI` | `VSCode & CLI`  |

### Files to change

Global find-and-replace across all `.md` files in `packages/kilo-docs/pages/` plus `SideNav.tsx` badges:

1. `"New Extension & CLI"` → `"VSCode (Pre-release) & CLI"` (do this FIRST — compound label, 31 instances)
2. `"New CLI & Extension"` → `"VSCode (Pre-release) & CLI"` (normalize variant, if any)
3. `"New Extension"` → `"VSCode (Pre-release)"` (standalone tab labels, AFTER compound is done)
4. `"Classic Extension"` → `"VSCode"` (tab labels)
5. SideNav `PlatformBadge`: "Classic" pill → "VSCode" pill, "New" pill → "Pre-release" pill
6. `PLAN-docs-version-switcher.md` tab naming convention table

### Prose references — MINIMAL CHANGE STRATEGY

**Both extensions are referred to as "the VSCode extension" in prose** (not "the Classic extension" or "the Pre-release extension"). The only place the distinction appears is:

- **Tab headings**: `VSCode` vs `VSCode (Pre-release)` vs `VSCode (Pre-release) & CLI`
- **Installation instructions**: Where users are told specifically to install the pre-release version from the marketplace

This means at GA, only the tab headings need to change (`VSCode` → `Legacy VSCode`, `VSCode (Pre-release)` → `VSCode`). No prose changes needed at GA.

Prose changes to make now:

**Generic prose (inside tabs, no distinction needed):**

- `"the Classic extension"` → `"the VSCode extension"`
- `"the New extension"` → `"the VSCode extension"`

**Contrastive prose (outside tabs, intro paragraphs and callouts that explain why tabs exist):**
Reference the tab labels directly since they sit right above the tab blocks:

- `"In the **Classic Extension**..."` → `"In the **VSCode** version..."`
- `"In the **New Extension & CLI**..."` → `"In the **VSCode (Pre-release) & CLI** version..."`
- `"The Classic Extension calls these **modes**"` → `"The **VSCode** version calls these **modes**"`
- `"the New Extension and CLI call them **agents**"` → `"the **VSCode (Pre-release) & CLI** version calls them **agents**"`

All ~15 contrastive references are outside tabs (intro paragraphs, callout boxes, shared sections between tab blocks). They introduce/explain the tabbed content below, so referencing tab labels is natural.

**Migration context:**

- `"from the Classic extension"` → `"from the VSCode extension"` (refers to current GA)

**Meta descriptions (frontmatter):**

- `"(Classic)"` → `"(VSCode)"`
- `"(New Extension & CLI)"` → `"(VSCode (Pre-release) & CLI)"`

---

## 4. Minimal Change Strategy

To keep this PR focused, the plan is:

### DO in this PR:

1. Fix config table (Category A) — factually wrong, small change
2. Fix broken anchor (Category B) — 1 line
3. Fix stale links to `/using-in-cli` (Category F) — 2 lines
4. Fix Agent Manager factual errors (Category D) — required for accuracy
5. Rename tab labels globally (item 3 above) — mechanical find-replace
6. Remove CLI prerequisite line from agent-manager.md

### DEFER to follow-up:

- KiloClaw nav path fix (Category C) — pre-existing, separate issue
- ChatGPT Plus/Pro guidance conflict — separate concern
- VersionContext fallback (Category E) — already outdated
- Expanding keyboard shortcuts table fully — low priority, can reference helper dialog for now

---

## Full Diff Review — Additional Factual Errors Found

Beyond the PR review comments, a thorough code-verified audit found **30+ additional factual errors**. Organized by severity:

### HIGH — Config/command won't work as documented

| #   | File                          | Line(s)        | Error                                                                                                       | Fix                                                     |
| --- | ----------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| 1   | `ai-providers/bedrock.md`     | 84-86          | Provider ID `"bedrock"` — actual is `"amazon-bedrock"`                                                      | Change provider ID                                      |
| 2   | `ai-providers/vertex.md`      | 55-57          | Env var `GOOGLE_CLOUD_REGION` doesn't exist — actual is `GOOGLE_CLOUD_LOCATION` or `GOOGLE_VERTEX_LOCATION` | Fix env var name                                        |
| 3   | `ai-providers/sap-ai-core.md` | 111-116        | All 4 env vars fabricated (`SAP_AI_CORE_*`) — actual is `AICORE_SERVICE_KEY` (JSON service key)             | Rewrite env var section                                 |
| 4   | `ai-providers/claude-code.md` | 59             | `claude auth login` doesn't exist — actual is `claude setup-token`                                          | Fix command                                             |
| 5   | `ai-providers/ollama.md`      | 99-112         | No `"ollama"` provider in CLI; config won't load models                                                     | Note custom provider setup or document correctly        |
| 6   | `kilocodeignore.md`           | 128-129        | Says "first matching rule wins" — code uses `findLast()` (last-match-wins)                                  | Reverse                                                 |
| 7   | `kilocodeignore.md`           | 54-64, 152-163 | `watcher.ignore` wrongly nested under `"config"` — should be top-level                                      | Remove wrapper                                          |
| 8   | `agent-manager.md`            | 124-135        | New Extension worktree path says `.kilocode/` — actual is `.kilo/`                                          | Fix path + directory tree (Classic tab is out of scope) |
| 10  | `using-in-kilo-code.md`       | 235            | "SSE Transport (Classic Extension Only)" — CLI also supports SSE as fallback                                | Remove "Classic Only"                                   |

### MEDIUM — Misleading or functionally wrong

| #   | File                    | Line(s)  | Error                                                                                                   | Fix                                     |
| --- | ----------------------- | -------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| 10  | `context-mentions.md`   | 142-143  | "Selection" and "Diagnostics" listed as automatic editor context — CONFIRMED neither in `EditorContext` | Remove rows or clarify code-action-only |
| 11  | `context-mentions.md`   | 128      | Claims "no special `@` syntax" — CONFIRMED both extension AND CLI TUI have `@`-mention autocomplete     | Acknowledge `@` mentions exist          |
| 12  | `chat-interface.md`     | 101      | Same `@`-mention claim                                                                                  | Same fix                                |
| 13  | `chat-interface.md`     | 115      | `--file` flag described generically — CONFIRMED only on `kilo run`, not TUI                             | Clarify "use `kilo run -f`"             |
| 14  | `context-mentions.md`   | 183      | Same `--file` issue                                                                                     | Same fix                                |
| 15  | `browser-use.md`        | 239      | Headless "(default: enabled)" — actual default is `false`                                               | Fix to "(default: disabled)"            |
| 16  | `using-in-kilo-code.md` | 166, 230 | Timeout default "5000" — actual is 30000ms                                                              | Fix                                     |
| 17  | `using-in-kilo-code.md` | 327-331  | MCP CLI commands table missing `logout` and `debug`                                                     | Add                                     |
| 18  | `claude-code.md`        | 70-86    | CLI tab shows Anthropic API config on Claude Code page                                                  | Clarify or fix                          |
| 19  | `agents-md.md`          | 186-189  | `KILO_DISABLE_EXTERNAL_SKILLS` described as disabling agent files — only disables skills                | Fix description                         |
| 20  | `custom-modes.md`       | 601, 816 | Claims `architect` has "native agent equivalent" — no such built-in                                     | Remove `architect`                      |
| 21  | `orchestrator-mode.md`  | 55       | Lists `docs` as built-in subagent — it's not                                                            | Fix examples                            |

### LOW — Minor inaccuracies, omissions, inconsistencies

| #   | File                        | Line(s) | Error                                                       | Fix                             |
| --- | --------------------------- | ------- | ----------------------------------------------------------- | ------------------------------- |
| 22  | `model-selection.md`        | 31      | Tab label `"New CLI"` vs `"CLI"`                            | Fix label                       |
| 23  | `checkpoints.md`            | 43      | `config.snapshot` should be just `snapshot`                 | Fix                             |
| 24  | `code-actions.md`           | 118-120 | Terminal action names abbreviated vs actual                 | Use exact labels                |
| 25  | `using-modes.md`            | 149     | `mcp` listed as built-in tool — MCP tools come from servers | Replace with "MCP server tools" |
| 26  | `auto-approving-actions.md` | 371-456 | `question` permission omitted                               | Add                             |
| 27  | `custom-instructions.md`    | 80-81   | `CONTEXT.md` listed without deprecation note                | Add note                        |

### Note: `~/.config/kilo/` path is correct on ALL platforms

The CLI uses the `xdg-basedir` library which resolves to `~/.config/` on all platforms (macOS, Linux, Windows) unless `XDG_CONFIG_HOME` is set. On Windows this becomes `C:\Users\<username>\.config\kilo\`. The earlier concern about macOS using `~/Library/Application Support/` was wrong — that's the native macOS convention but `xdg-basedir` does NOT follow it. The documented `~/.config/kilo/` path is correct everywhere.

For Windows, we should add `C:\Users\<username>\.config\kilo\` alongside the `~/.config/kilo/` reference (or just note "On Windows: `%USERPROFILE%\.config\kilo\`").

---

### Removed from scope (Classic/legacy extension only)

These errors are in Classic Extension tabs — out of scope per the principle of not changing legacy content:

- `agent-manager.md:108` — Classic worktree path (says `.kilo/`, should be `.kilocode/`)
- `using-in-kilo-code.md:243-255` — Classic SSE example missing `"type": "sse"` field
- `auto-approving-actions.md:412` — "Most tools default to allow" claim (CONFIRMED CORRECT — `agent.ts` ships `"*": "allow"` base)

### Removed: not 100% confident or out-of-scope

- `ai-providers/claude-code.md:59` — `claude auth login` vs `claude setup-token` (external tool, not our codebase)
- `ai-providers/ollama.md:99-112` — unclear if custom provider setup is documented intentionally
- `ai-providers/anthropic.md:49-56` — `"env"` config technically works, just suboptimal

---

## Legacy Content Changes Found in Diff (Principle Violation)

The full diff review found the PR **modifies existing content** (not just wraps it in tabs) in several places. Per the "only document new extension/CLI" principle, these should be reverted or flagged:

### Must-revert (factual changes to shared/Classic content)

| File             | Issue                                                                                                                                                                                                                               |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `checkpoints.md` | **Encoding bug**: `→` corrupted to `â`. Shared "Technical Implementation" changed from Classic-accurate ("Git commits", `simple-git`) to new-platform ("Git tree objects", "Snapshot Service"). These should be tabbed, not shared. |
| `groq.md`        | "Tips and Notes" section (4 bullet points) deleted entirely — content loss, not moved to any tab                                                                                                                                    |

### Should-flag (content generalizations that may lose Classic-specific value)

| File                    | Issue                                                                                                    |
| ----------------------- | -------------------------------------------------------------------------------------------------------- |
| `chat-interface.md`     | Shared "What makes requests work" tips removed `@`-mention guidance — Classic users lose helpful context |
| `installing.md`         | Pre-release tab content rewritten; feature parity tracking link removed                                  |
| `agent-manager.md`      | Cancel vs Stop distinction removed from both tabs                                                        |
| `context-condensing.md` | Shared overview generalized from "Context Condensing" to "Context management features"                   |
| `custom-modes.md`       | Title, headings, shared descriptions generalized; "Sticky Models" moved to Classic-only                  |

### Acceptable (tab-wrapping with minor formatting changes)

Files like `custom-rules.md`, `custom-instructions.md`, `skills.md`, `kilocodeignore.md`, `code-actions.md`, `using-modes.md`, `quickstart.md` — mostly just tab-wrapping with minor cleanup. These are fine.

---

## Follow-Up Plan (post-PR)

Items deferred from this PR for separate follow-up:

1. KiloClaw nav path active-state issue (pre-existing)
2. ChatGPT Plus/Pro guidance conflict between pages
3. Full keyboard shortcuts audit for Agent Manager
4. Review all 30 AI provider pages for provider ID / env var accuracy (only 6 checked in depth)
5. Verify Classic extension content wasn't unintentionally changed in the generalizations flagged above

---

## Implementation Steps

### Step 1: Global tab label rename (mechanical find-replace)

Order matters — do compound labels first to avoid partial matches:

1. `{% tab label="New Extension & CLI" %}` → `{% tab label="VSCode (Pre-release) & CLI" %}` (31 instances)
2. `{% tab label="New CLI & Extension" %}` → `{% tab label="VSCode (Pre-release) & CLI" %}` (if any)
3. `{% tab label="New Extension" %}` → `{% tab label="VSCode (Pre-release)" %}` (remaining standalone)
4. `{% tab label="Classic Extension" %}` → `{% tab label="VSCode" %}` (all instances)
5. SideNav `PlatformBadge` in `SideNav.tsx`: "Classic" → "VSCode", "New" → "Pre-release"

### Step 2: Prose references (~30 instances)

- Update contrastive intro paragraphs to reference tab labels: "the **VSCode** version" / "the **VSCode (Pre-release) & CLI** version"
- Update generic in-tab prose: "the Classic/New extension" → "the VSCode extension"
- Update migration references: "from the Classic extension" → "from the VSCode extension"
- Update frontmatter descriptions

### Step 3: Fix config table (`using-in-kilo-code.md:38-43`)

Replace with correct filenames: `kilo.jsonc` as recommended, list all supported names, confirm `~/.config/kilo/`

### Step 4: Fix broken anchor (`server-transports.md:199`)

`#understanding-transport-types` → `#transport-types`

### Step 5: Fix stale links

- `automate/index.md` and `cli.md`: `/using-in-cli` → `/using-in-kilo-code`

### Step 6: Rewrite `agent-manager.md` VSCode (Pre-release) tab content

- Remove CLI prerequisite (line 29) — CLI is bundled
- Fix worktree paths: `.kilocode/worktrees/` → `.kilo/worktrees/`
- Replace "Parallel Mode" with worktree-centric architecture:
  - Users create worktrees from New Worktree dialog
  - Multi-version mode: 1-4 worktrees with same prompt, optional model comparison ("Compare Models")
  - Local card: represents user's actual workspace (sessions without worktrees)
  - Session promotion: create a worktree from an existing session
- Fix cleanup: "cleaned up automatically" → manual removal (trash icon with confirmation), stale detection via poller
- Code review: mention button-based flow to open review tab with inline commenting, `Cmd+Enter` to send comments to chat
- Shortcuts: expand table OR add reference to shortcut helper dialog (`Cmd+Shift+/`)
- Remove shared intro line about "Parallel Mode" (line 13)

### Step 7: Update `PLAN-docs-version-switcher.md` tab naming convention
