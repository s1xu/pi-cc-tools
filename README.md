# pi-claude-code-ui

> [!NOTE]
> **Personal fork** by [@s1xu](https://github.com/s1xu) of [`pi-cc-tools`](https://github.com/FammasMaz/pi-cc-tools).
> This fork ships opinionated, Claude Code–style defaults out of the box (dsh-style
> user messages/input prompt/thinking, transparent tool backgrounds, bundled
> `claude-code-dark` theme, and more) — every default can still be opted out of in
> `settings.json`. See the [Fork defaults](#fork-defaults-baked-in--no-config-needed) section below.

> [!IMPORTANT]
> **Package renamed in 1.0.69.** This project is now published as [`pi-claude-code-ui`](https://www.npmjs.com/package/pi-claude-code-ui) (was `pi-claude-style-tools`).
>
> Install / migrate:
> ```bash
> pi install npm:pi-claude-code-ui
> # or
> npm i pi-claude-code-ui
> ```
> See [CHANGELOG 1.0.69](./CHANGELOG.md#1069--2026-07-17) for the full release notes (status dots, bare branch connectors, and more).

Claude Code inspired tool rendering for Pi — Shiki-powered diffs, status dots, branch connectors, file icons, and configurable output modes.

## Features

- **Compact built-in tool rendering** for `read`, `bash`, `grep`, `find`, `ls`, `edit`, and `write`
- **Claude-style OpenAI tool rendering** for `apply_patch` plus common Pi/OpenAI-style tools like `webfetch`, `web_search`, `fetch_content`, task tools, and context tools
- **`apply_patch` diff previews** that render parsed file patches in the call phase, similar to `edit`/`write`
- **Adaptive edit/write diffs** with split or unified layouts, syntax highlighting, and inline word-level emphasis
- **Diff stat bar** with colored add/remove summary and hunk metadata
- **Progressive collapsed diff hints** that shorten on narrow terminals
- **Thinking labels** during streaming and final messages, with context sanitization
- **MCP-aware rendering** with hidden, summary, and preview modes
- **Configurable output modes** for read, search, bash, and MCP results
- **Live running previews** that show a few output lines for active tool calls (latest lines for bash), persisting until the next tool/text activity
- **Subagent completion notifications** restyled to match the same Claude-style tool rows
- **RTK rewrite integration** that folds rewrite notices into the bash tool row with a muted `(RTK)` badge and expanded-only rewrite details
- **Transparent tool backgrounds** in `transparent` or `outlines` mode
- **Theme-adaptive palette** — borders, branch connectors, dim text, spinner accent, and diff backgrounds automatically follow the active pi theme (set `themeAdaptive: false` to keep the fixed Claude-style palette)
- **Light Ghostty-sync themes** — edit/write diffs use `github-light` highlighting and light-tinted diff rows; tool pending dots use softer chrome colors
- **Transparent edit/write diffs** with universal red/green diff colors
- **Grouped consecutive tool calls** with a compact status header and per-tool glance rows (set `groupToolCalls: false` to disable)
- **Extra detail toggle** with `Ctrl+Shift+O`, increasing expanded preview caps without making the default view heavy
- **Global border patch** for all tool rows, including unknown/custom tools

## Configuration

> **This fork ships opinionated defaults** (see below) — you don't need to set
> anything to get the Claude Code dark look. All settings are still available
> to override in `.pi/settings.json` or `~/.pi/settings.json`.

```json
{
  "toolBackground": "transparent",
  "readOutputMode": "preview",
  "searchOutputMode": "preview",
  "mcpOutputMode": "preview",
  "previewLines": 8,
  "expandedPreviewMaxLines": 4000,
  "extraExpandedPreviewMaxLines": 12000,
  "extraToolOutputExpanded": false,
  "groupToolCalls": true,
  "bashOutputMode": "opencode",
  "bashCollapsedLines": 24,
  "liveToolPreview": true,
  "liveToolPreviewLines": 5,
  "diffCollapsedLines": 24,
  "themeAdaptive": true,
  "diffTheme": "claude-code-dark"
}
```

### Fork defaults (baked in — no config needed)

These are the hardcoded defaults in this fork, so the UI matches Claude Code
out of the box. Set any of them to `false`/another value to opt out:

| Setting | Fork default | Effect |
|---------|-------------|--------|
| `hideThinkingBlock` | `false` | Thinking blocks are shown (pi default; required for dsh thinking) |
| `dshStyleThinking` | `true` | Thinking expands while streaming, collapses to `∴ Thinking · Ns` after |
| `dshStyleUserMessage` | `true` | `❯ text` user bubble on `userMessageBg`, no rounded border box |
| `dshUserMessageBg` | `"theme"` | Background color for the user message bubble (`"theme"` = follow current theme, `"transparent"` = no box) |
| `dshStyleInputPrompt` | `true` | `❯ ` before the editor text (dsh-TUI PromptInput look) |
| `dshStyleSimpleCodeBlocks` | `true` | Fenced code blocks render as plain markdown (no rounded box) |
| `dshStylePlainDiff` | `true` | Diffs keep red/green fg but drop tinted row backgrounds |
| `toolBackground` | `transparent` | Transparent tool backgrounds |
| `bashCollapsedLines` | `24` | Lines for collapsed bash output |
| `liveToolPreview` | `true` | Live output preview while tools are still running |
| `diffTheme` | `claude-code-dark` | Default diff palette preset (see below) |
| `autoClaudeDarkTheme` | `true` | Auto-selects the bundled `claude-code-dark` pi theme when the current theme is a built-in default |

### Bundled `claude-code-dark` theme

The fork ships the [`claude-code-dark`](themes/claude-code-dark.json) pi theme
and auto-installs it into Pi's user themes directory on first load, so it is
available to Pi's startup theme resolution without conflicts. On first load,
if your active pi theme is a built-in
`dark`/`light` default, the fork auto-switches to `claude-code-dark`. Set
`autoClaudeDarkTheme: false` in settings to keep your own theme, or pick the
theme manually:

```bash
# settings.json
{
  "theme": "claude-code-dark"
}
```

With the claude-code-dark theme active and `themeAdaptive: true` (default), the
tool chrome, diff accents, and spinner colors all derive from that theme.

### Theme integration

When `themeAdaptive` is `true` (default), the following colors are derived from the active pi theme on every render and re-derived whenever the theme changes:

| Element | Derived from |
|---------|--------------|
| User box, tool rules, code fences | `dim` → `muted` → `borderMuted` → `thinkingText` |
| Branch connectors (`├`, `└`, `│`) | **fixed rgb(72)** by default (theme-independent); `/cc-tools branch theme` to follow pi theme |
| "✻ Turn took Ns" line (final message only, with session total + turn count) | `muted` |
| Thinking-block text and `∴` marker (marker hidden when thinking is collapsed) | `muted` |
| Diff add/remove accents | `toolDiffAdded` / `toolDiffRemoved` |
| Diff background tints | mixed against `toolSuccessBg` base |
| Spinner verb text (`Working…`) | `borderAccent` (fallback: `accent`) |
| Spinner status text | `muted` |

User-supplied `diffTheme` presets and `diffColors` overrides always win over theme-derived defaults. File-type icons (e.g. `ts`, `py`, `rs`) keep their language-identity colors and are not theme-derived.

Set `themeAdaptive: false` to keep the original fixed Claude-style palette regardless of the active pi theme.

On `/resume`, `/new`, or `/fork`, tool chrome is rebound from the **current** pi theme (no coupling to Ghostty or other theme extensions). If you use Ghostty sync, listing it **above** this extension in `settings.json` is recommended so `setTheme` runs before chrome rebind.

#### Toggle at runtime with `/cc-theme`

```text
/cc-theme           # show current setting + theme name
/cc-theme status    # show current setting + color preview (incl. spinner)
/cc-theme on        # follow pi theme
/cc-theme off       # keep fixed Claude palette
/cc-theme toggle    # flip the current value
```

The selection is persisted to `~/.pi/settings.json` and applied to the next rendered tool row. No restart required.

#### Repaint the spinner with `/cc-spinner`

The spinner glyph itself is still colored by pi's loader using `accent`, while the verb text (e.g. `Cooking…`) follows `borderAccent` by default so it stays lively without being the exact same color as the glyph. The status suffix (e.g. `(thinking · ↓ 10 tokens · 2s)`) follows `muted`. Use `/cc-spinner` to bind either text element to any other theme color key:

```text
/cc-spinner preview          # list every common theme key with a colored sample
/cc-spinner verb <key>       # change the verb color (e.g. thinkingHigh, mdHeading)
/cc-spinner status <key>     # change the status suffix color
/cc-spinner reset            # restore defaults (verb=borderAccent, status=muted)
```

The selection is persisted as `spinnerVerbColor` / `spinnerStatusColor` in `~/.pi/settings.json` and applied on the next spinner tick.

### Tool background modes

| Value | Behavior |
|-------|----------|
| `default` | Standard Pi tool backgrounds |
| `transparent` | Transparent tool backgrounds (**fork default**) |
| `outlines` | Transparent backgrounds with top/bottom border lines |

Use `/cc-tools` to control tool UI at runtime:

```text
/cc-tools status          # show style, grouping, and extra-detail state
/cc-tools transparent     # tool style: outlines, transparent, or default
/cc-tools group toggle    # toggle grouped adjacent/concurrent tool calls
/cc-tools group off       # disable grouping (also ungroups current grouped rows)
/cc-tools detail toggle   # same mode as Ctrl+Shift+O
```

### Output modes

| Setting | Values | Default |
|---------|--------|---------|
| `readOutputMode` | `hidden`, `summary`, `preview` | `preview` |
| `searchOutputMode` | `hidden`, `count`, `preview` | `preview` |
| `mcpOutputMode` | `hidden`, `summary`, `preview` | `preview` |
| `bashOutputMode` | `opencode`, `summary`, `preview` | `opencode` |

### Display settings

| Setting | Default | Description |
|---------|---------|-------------|
| `previewLines` | `8` | Lines shown in collapsed preview mode |
| `expandedPreviewMaxLines` | `4000` | Max lines when expanded with Ctrl+O |
| `extraExpandedPreviewMaxLines` | `12000` | Max lines after Ctrl+Shift+O extra-detail mode |
| `extraToolOutputExpanded` | `false` | Start with Ctrl+Shift+O extra-detail mode enabled |
| `groupToolCalls` | `true` | Group adjacent/concurrent tool calls under a compact status header |
| `bashCollapsedLines` | `24` | Lines for collapsed bash output (fork default) |
| `liveToolPreview` | `true` | Show a small live output preview while tools are still running |
| `liveToolPreviewLines` | `5` | Lines shown in the collapsed live preview |
| `diffCollapsedLines` | `24` | Diff lines before collapsing |
| `diffTheme` | `claude-code-dark` | Diff preset (`default`, `midnight`, `neon`, `claude-code-dark`, …). Also accepts Shiki theme names like `github-dark` |
| `dshStyleThinking` | `true` | (Requires pi `hideThinkingBlock: false`) dsh-TUI-style thinking: expanded while streaming, collapses to `∴ Thinking · Ns (ctrl+o to expand)` after completion. Ctrl+O also expands/collapses thinking summaries (pi's tool-expansion loop now includes assistant messages). |
| `dshStyleUserMessage` | `true` | dsh-TUI-style user messages: `❯ text` on the theme's grey `userMessageBg` background with no rounded border box. |
| `dshUserMessageBg` | `"theme"` | User bubble background: `"theme"` follows the active theme's `userMessageBg` (transparent if unset), `"transparent"` renders `❯ text` with no box (handy when the terminal flips to a light scheme). |
| `dshStyleInputPrompt` | `true` | `❯ ` before the editor text (dsh-TUI PromptInput look). |
| `dshStyleSimpleCodeBlocks` | `true` | Fenced code blocks render as plain markdown (no rounded border box). |
| `dshStylePlainDiff` | `true` | Diffs keep red/green fg colors but drop the tinted row backgrounds. |
| `autoClaudeDarkTheme` | `true` | Auto-select the bundled `claude-code-dark` theme when on a built-in default theme. |

## Notes

This package targets recent Pi versions where tool renderers use:

- `renderCall(args, theme, context)`
- `renderResult(result, { expanded, isPartial }, theme, context)`

Unknown/custom tools do not have a public global renderer hook in Pi, so this package patches container rendering to add top/bottom borders for all tool executions in border mode.

## Credits

This project builds upon and was inspired by the excellent work of:

- **[@heyhuynhgiabuu/pi-pretty](https://github.com/buddingnewinsights/pi-pretty)** by [huynhgiabuu](https://github.com/buddingnewinsights) — Pretty terminal output with syntax-highlighted file reads, colored bash output, and tree-view directory listings
- **[@heyhuynhgiabuu/pi-diff](https://github.com/buddingnewinsights/pi-diff)** by [huynhgiabuu](https://github.com/buddingnewinsights) — Shiki-powered terminal diff renderer with word-level diffs in split and unified views
- **[pi-tool-display](https://github.com/MasuRii/pi-tool-display)** by [MasuRii](https://github.com/MasuRii) — Compact tool call rendering, diff visualization, and output truncation
