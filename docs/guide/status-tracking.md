---
description: Display agent status in your tmux window list for at-a-glance visibility
---

# Status tracking

Workmux can display the status of the agent in your tmux window list, giving you at-a-glance visibility into what the agent in each window is doing.

<div style="display: flex; justify-content: center; margin: 1.5rem 0;">
  <img src="/status.webp" alt="tmux status showing agent icons" style="border-radius: 4px;">
</div>

## Agent support

| Agent       | Status                                                                 |
| ----------- | ---------------------------------------------------------------------- |
| Claude Code | ✅ Supported                                                           |
| OpenCode    | ✅ Supported                                                           |
| Gemini CLI  | [In progress](https://github.com/google-gemini/gemini-cli/issues/9070) |
| Codex       | ✅ Supported (via hooks)                                               |

## Status icons

- 🤖 = agent is working
- 💬 = agent is waiting for user input
- ✅ = agent finished (auto-clears on window focus)

## Claude Code setup

Install the workmux status plugin:

```bash
claude plugin marketplace add raine/workmux
claude plugin install workmux-status
```

Alternatively, you can manually add the hooks to `~/.claude/settings.json`. See [.claude-plugin/plugin.json](https://github.com/raine/workmux/blob/main/.claude-plugin/plugin.json) for the hook configuration.

Workmux automatically modifies your tmux `window-status-format` to display the status icons. This happens once per session and only affects the current tmux session (not your global config).

## OpenCode setup

Download the workmux status plugin to your global OpenCode plugin directory:

```bash
mkdir -p ~/.config/opencode/plugin
curl -o ~/.config/opencode/plugin/workmux-status.ts \
  https://raw.githubusercontent.com/raine/workmux/main/.opencode/plugin/workmux-status.ts
```

Restart OpenCode for the plugin to take effect.

## Codex setup

Add the following to `~/.codex/config.toml`:

```toml
[hooks]
turn_started = [["workmux", "set-window-status", "working"]]

# Approval / user-input prompts
exec_approval_request = [["workmux", "set-window-status", "waiting"]]
apply_patch_approval_request = [["workmux", "set-window-status", "waiting"]]
elicitation_request = [["workmux", "set-window-status", "waiting"]]

# Clear “waiting” on first activity after approval / input
exec_command_output_delta = [["workmux", "set-window-status", "working"]]
exec_command_end = [["workmux", "set-window-status", "working"]]
patch_apply_begin = [["workmux", "set-window-status", "working"]]
mcp_tool_call_begin = [["workmux", "set-window-status", "working"]]
web_search_begin = [["workmux", "set-window-status", "working"]]

# Turn end
turn_complete = [["workmux", "set-window-status", "done"]]
turn_aborted = [["workmux", "set-window-status", "done"]]
error = [["workmux", "set-window-status", "done"]]
```

## Customization

You can customize the icons in your config:

```yaml
# ~/.config/workmux/config.yaml
status_icons:
  working: "🔄"
  waiting: "⏸️"
  done: "✔️"
```

If you prefer to manage the tmux format yourself, disable auto-modification and add the status variable to your `~/.tmux.conf`:

```yaml
# ~/.config/workmux/config.yaml
status_format: false
```

```bash
# ~/.tmux.conf
set -g window-status-format '#I:#W#{?@workmux_status, #{@workmux_status},}#{?window_flags,#{window_flags}, }'
set -g window-status-current-format '#I:#W#{?@workmux_status, #{@workmux_status},}#{?window_flags,#{window_flags}, }'
```
