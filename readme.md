## CCSumatraPDF - SumatraPDF with Claude Code Integration

A fork of [SumatraPDF](https://github.com/sumatrapdfreader/sumatrapdf) that integrates [Claude Code](https://docs.anthropic.com/en/docs/claude-code) as a sidebar chat panel, allowing you to interact with Claude AI directly while reading documents.

### Features

- **Sidebar Chat Panel**: Press `Ctrl+Shift+E` to toggle a Claude Code chat sidebar on the right side of the document viewer.
- **Per-Tab Sessions**: Each document tab maintains its own independent Claude conversation session. Switching tabs restores the corresponding chat history.
- **Session Management**: A dropdown combo box lets you select from existing sessions or create new ones. Session names include the PDF filename and path for easy identification.
- **Markdown Rendering**: Claude's responses are rendered as rich markdown (with syntax highlighting) via WebView2 and [marked.js](https://github.com/markedjs/marked).
- **Streaming Output**: Uses Claude CLI's `stream-json` output format to display responses in real-time, including intermediate tool calls and results.
- **Model & Effort Selection**: Choose between Sonnet, Opus, and Haiku models, and set thinking effort (Low / Medium / High / Max) from the sidebar UI.
- **Skip Permissions Toggle**: Optional checkbox to run Claude with `--dangerously-skip-permissions` for unattended operation.
- **Stop Button**: Interrupt a running Claude agent at any time.
- **Persistent Settings**: Model, effort, and permission preferences are saved across app restarts.

### Prerequisites

- **Claude CLI** must be installed and available. The app searches for `claude.exe` in:
  - `~/.local/bin/claude.exe` (npm global install)
  - `~/.claude/local/claude.exe`
  - System PATH
- **WebView2 Runtime** (usually pre-installed on Windows 10/11).

### Building

Requires Visual Studio 2022 or 2026 with C++ desktop development workload.

```bash
# Generate VS project files
bun ./cmd/premake.ts

# Build debug
bun ./cmd/build.ts
```

The output executable is `./out/dbg64/CCSumatraPDF.exe`.

### Architecture

The Claude Code integration is implemented in two files:

- **`src/ClaudeCode.cpp`** (~1400 lines) - Complete sidebar implementation including:
  - Win32 UI (combo boxes, input field, buttons, splitter, WebView2)
  - Claude CLI process management (`CreateProcessW` with piped stdout)
  - Stream-JSON parsing for real-time response display
  - Session discovery and history loading from `~/.claude/projects/` JSONL files
  - Settings persistence to `CCSumatraPDF-claude.txt`
- **`src/ClaudeCode.h`** - Public API (`CreateClaudePanel`, `ToggleClaudePanel`, `DestroyClaudePanel`, `OnClaudeTabChanged`, `RelayoutForClaudeSplitter`)

Other modified files:
- `src/MainWindow.h` - Added sidebar widget handles to `MainWindow` struct
- `src/WindowTab.h/.cpp` - Added per-tab session ID, chat log, and process handle
- `src/SumatraPDF.cpp` - Sidebar creation, layout integration, command handler
- `src/Tabs.cpp` - Tab switch hook for session switching
- `src/Accelerators.cpp` - `Ctrl+Shift+E` keybinding
- `cmd/gen-commands.ts` / `src/Commands.h/.cpp` - `CmdClaudeCode` command registration
- `premake5.lua` - Output renamed to `CCSumatraPDF.exe`
- `premake5.files.lua` - Added `ClaudeCode.*` to source file list

### Original SumatraPDF

SumatraPDF is a multi-format (PDF, EPUB, MOBI, CBZ, CBR, FB2, CHM, XPS, DjVu) reader
for Windows under (A)GPLv3 license, with some code under BSD license (see AUTHORS).

* [Website](https://www.sumatrapdfreader.org/free-pdf-reader)
* [Manual](https://www.sumatrapdfreader.org/manual)
* [Developer Information](https://www.sumatrapdfreader.org/docs/Contribute-to-SumatraPDF)
