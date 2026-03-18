---
title: "Agent Manager"
description: "Manage and orchestrate multiple AI agents"
---

# Agent Manager

The Agent Manager is a dedicated panel for running, supervising, and orchestrating multiple Kilo Code agent sessions from within VS Code. Both the Classic and New extensions include an Agent Manager, but with different underlying architectures and feature sets.

**Core capabilities shared by both extensions:**

- Start new sessions and resume existing ones
- Parallel Mode with Git worktree isolation for safe, independent changes
- Per-session terminals
- Setup scripts for environment configuration
- Remote/cloud-synced sessions filtered to your current repository

## Prerequisites

{% tabs %}
{% tab label="Classic Extension" %}

- Open a project in VS Code (workspace required)
- Authentication: Sign in through the extension settings

{% /tab %}
{% tab label="New Extension" %}

- Install/update the Kilo Code CLI (latest) — see [CLI setup](/docs/code-with-ai/platforms/cli)
- Open a `git` enabled project in VS Code (workspace required)
- Authentication: You must be logged in via the extension settings OR use CLI with kilocode as provider (see [Authentication Requirements](#authentication-requirements))

{% /tab %}
{% /tabs %}

## Opening the Agent Manager

{% tabs %}
{% tab label="Classic Extension" %}

- Command Palette: "Kilo Code: Open Agent Manager"
- The panel opens as a webview with a session sidebar and detail view

{% /tab %}
{% tab label="New Extension" %}

- Command Palette: "Kilo Code: Open Agent Manager"
- Keyboard shortcut: `Cmd+Shift+M` (macOS) / `Ctrl+Shift+M` (Windows/Linux)
- The panel opens as a webview and stays active across focus changes
- Tabs can be reordered via drag-and-drop

{% /tab %}
{% /tabs %}

## Sending Messages and Controls

{% tabs %}
{% tab label="Classic Extension" %}

- Type a message to start or continue a conversation
- Approvals: If the agent requests tool use, the UI shows an approval prompt — approve or reject
- Cancel the running agent to stop it cooperatively

{% /tab %}
{% tab label="New Extension" %}

- Send follow-up messages to the running agent at any time
- Approvals: If the agent asks to use a tool, run a command, launch the browser, or connect to an MCP server, the UI shows an approval prompt
  - Approve or reject, optionally adding a short note

{% /tab %}
{% /tabs %}

## Resuming Sessions

You can continue a session later (local or remote) on both extensions.

{% tabs %}
{% tab label="Classic Extension" %}

- Select a previous session from the sidebar to resume it
- The extension spawns a new agent process attached to that session
- Labels from the original session are preserved

{% /tab %}
{% tab label="New Extension" %}

- Labels from the original session are preserved whenever possible
- Your first follow-up message becomes the continuation input
- You can also fork an existing session to create a new branch of exploration, or promote a session to become the active working session

{% /tab %}
{% /tabs %}

## Parallel Mode and Worktrees

Parallel Mode runs the agent in an isolated Git worktree branch, keeping your main branch clean.

{% tabs %}
{% tab label="Classic Extension" %}

- Enable the "Parallel Mode" toggle before starting a session
- The extension creates and manages worktrees automatically
- Cannot be used from inside an existing worktree — open the main repository

### Worktree Location

Worktrees are created in `.kilo/worktrees/` within your project directory, automatically excluded from git via `.git/info/exclude`.

### After Completion

- The worktree is cleaned up automatically, but the branch is preserved
- Review and merge the branch in your VCS UI

{% /tab %}
{% tab label="New Extension" %}

- Enable the "Parallel Mode" toggle before starting
- The extension prevents using Parallel Mode inside an existing worktree
  - Open the main repository (where `.git` is a directory) to use this feature

### Worktree Location

Worktrees are created in `.kilocode/worktrees/` within your project directory. This folder is automatically excluded from git via `.git/info/exclude` (a local-only ignore file that doesn't require a commit).

```
your-project/
├── .git/
│   └── info/
│       └── exclude   # local ignore rules (includes .kilocode/worktrees/)
├── .kilocode/
│   └── worktrees/
│       └── feature-branch-1234567890/   # isolated working directory
└── ...
```

### While Running

The Agent Manager surfaces:

- Branch name created/used
- Worktree path
- A completion/merge instruction message when the agent finishes

### After Completion

- The worktree is cleaned up automatically, but the branch is preserved
- Review the branch in your VCS UI
- Merge or cherry-pick the changes as desired

### Resuming Parallel Sessions

If you resume a Parallel Mode session later, the extension will:

1. Reuse the existing worktree if it still exists
2. Or recreate it from the session's branch

{% /tab %}
{% /tabs %}

## Diff Panel and Code Review

{% tabs %}
{% tab label="Classic Extension" %}

Session changes are visible through standard VS Code source control. The Classic extension does not include a built-in diff panel.

{% /tab %}
{% tab label="New Extension" %}

The New extension includes a rich diff panel for reviewing agent-produced changes:

- **Open the diff panel:** `Cmd+D` (macOS) / `Ctrl+D` (Windows/Linux), or via the toolbar
- **Full-screen diff view** for focused review
- **Apply workflow:** Accept or reject individual file changes with conflict resolution support
- **Code review annotations:** Leave inline comments on agent-produced diffs
- **Multi-model comparison:** Compare up to 4 model outputs side-by-side for the same prompt to evaluate different approaches

{% /tab %}
{% /tabs %}

## Import from GitHub PR

{% tabs %}
{% tab label="Classic Extension" %}

Not available in the Classic extension.

{% /tab %}
{% tab label="New Extension" %}

Paste a GitHub Pull Request URL to import the PR context into a new Agent Manager session. This allows you to continue working on or reviewing a PR directly within the Agent Manager.

{% /tab %}
{% /tabs %}

## Keyboard Shortcuts (New Extension)

The New Extension includes dedicated keyboard shortcuts:

| Shortcut                      | Action                    |
| ----------------------------- | ------------------------- |
| `Cmd+Shift+M`                 | Open Agent Manager        |
| `Cmd+Alt+Up` / `Cmd+Alt+Down` | Navigate between sessions |
| `Cmd+D`                       | Toggle diff panel         |

(On Windows/Linux, replace `Cmd` with `Ctrl`.)

## Authentication Requirements

The Agent Manager requires proper authentication for full functionality, including session syncing and cloud features.

### Supported Authentication Methods

1. **Kilo Code Extension (Recommended)**
   - Sign in through the extension settings
   - Provides seamless authentication for the Agent Manager
   - Enables session syncing and cloud features

2. **CLI with Kilo Code Provider** (New extension only)
   - Use the CLI configured with `kilocode` as the provider
   - Run `kilocode config` to set up authentication
   - See [CLI setup](/docs/code-with-ai/platforms/cli) for details

### BYOK Limitations

**Important:** Bring Your Own Key (BYOK) is not fully supported with the Agent Manager.

If you're using BYOK with providers like Anthropic, OpenAI, or OpenRouter:

- The Agent Manager will not have access to cloud-synced sessions
- Session syncing features will be unavailable
- You must use one of the supported authentication methods above for full functionality

To use the Agent Manager with all features enabled, switch to the Kilo Code provider or sign in through the extension.

## Remote Sessions (Cloud)

When signed in (Kilo Cloud), the Agent Manager lists your recent cloud-synced sessions:

{% tabs %}
{% tab label="Classic Extension" %}

- Sessions are fetched and filtered to the current repository
- Select a remote session to view its transcript and continue the work locally

{% /tab %}
{% tab label="New Extension" %}

- Up to 50 sessions are fetched
- Sessions are filtered to the current repository via normalized Git remote URL
  - If the current workspace has no remote, only sessions without a git_url are shown
- Selecting a remote session loads its message transcript
- To continue the work locally, send a message — the Agent Manager will spawn a local process bound to that session

Message transcripts are fetched from a signed blob and exclude internal checkpoint "save" markers as chat rows (checkpoints still appear as dedicated entries in the UI).

{% /tab %}
{% /tabs %}

## Troubleshooting

{% tabs %}
{% tab label="Classic Extension" %}

- "Please open a folder…" error — the Agent Manager requires a VS Code workspace folder
- "Cannot use parallel mode from within a git worktree" — open the main repository, not a worktree checkout
- Remote sessions not visible — ensure you're signed in and the repo's remote URL matches
- Authentication errors — verify you're logged in via extension settings

{% /tab %}
{% tab label="New Extension" %}

- CLI not found or outdated
  - Install/update the CLI: [CLI setup](/docs/code-with-ai/platforms/cli)
  - If you see an "unknown option --json-io" error, update to the latest CLI
- "Please open a folder…" error — the Agent Manager requires a VS Code workspace folder
- "Cannot use parallel mode from within a git worktree" — open the main repository (where `.git` is a directory), not a worktree checkout
- Remote sessions not visible
  - Ensure you're signed in and the repo's remote URL matches the sessions you expect to see
  - If using BYOK, session syncing is not available — switch to Kilo Code provider or sign in through the extension
- Authentication errors
  - Verify you're logged in via extension settings or using CLI with kilocode provider

{% /tab %}
{% /tabs %}

## Related Features

- [Sessions](/docs/collaborate/sessions-sharing)
- [Auto-approving Actions](/docs/getting-started/settings/auto-approving-actions)
- [CLI](/docs/code-with-ai/platforms/cli)
