[中文](./README_zh.md)

# tf-assistant

**tf-assistant** is a macOS desktop application that brings together an AI coding agent, speech transcription, screenshot OCR, translation, and subtitle editing — powered by local AI models.

## Features

### AI Coding Agent
An Agentic Development Environment (ADE) built on the opencode coding agent, in a dedicated Agent window.

- Chat with an AI agent inside your repositories: multi-turn streaming with text, reasoning, tool-call, todo-list, image, and plan-review parts
- Helmor-style composer with model picker (grouped by provider), reasoning-effort picker, agent picker (build / plan / custom), and a live-updating theme-aware context-usage ring
- Repository & workspace model: add local repos or clone from URL, then work in four workspace modes — `worktree` (git worktree checkout), `local` (operate in place), `chat` (scratch), and `non_git` (plain directory)
- Right-sidebar file browser: filesystem-watched directory tree with auto-refresh, Git status badges, workspace-wide ripgrep file search, read-only preview (incl. inline image/audio/video), and full file ops (new, rename, delete, reveal)
- Inline Git panel: branch switcher, ahead/behind + staged/unstaged status summary, per-file worktree diffs, and recent commits
- Embedded collapsible terminal panel: multiple plain-shell terminals per workspace, kept alive while collapsed
- Dedicated file editor window with multi-file tabs that track filesystem changes: Monaco for code, Milkdown WYSIWYG for Markdown, optional Vim mode, font-size/font-family controls, and find/replace
- LSP-powered code navigation in the editor: go-to-definition (F12 / Cmd+Click), inline definition peek (Alt+Click), find all references (Shift+F12), hover for type signatures and docs, in-file and workspace-wide symbol search, back/forward jump history, and Vim `gd`/`du` bindings
- Safety-first permissions: every bash command asks, secret-file reads ask, the `plan` agent stays read-only, with persistent "always allow" rules and an auto-grant toggle
- Interactive question cards: step through the agent's questions with options, free-text answers, and Decline/Submit
- Slash-command discovery (`/`), message revert/unrevert, virtualized long threads, and server-health heartbeat recovery
- Per-workspace MCP configuration UI layered on your global opencode config (new servers default to disabled)

### Speech-to-Text
- Transcribe audio files (WAV, MP3, FLAC, M4A, AAC, and more)
- Download and transcribe YouTube videos
- Real-time system capture with live captions
- Speaker diarization (identify who spoke when)
- Multiple transcription modes: voice activity detection, fixed windows, or full diarization
- Export transcripts as SRT

### AI Dictation
- Dictate into any app: speak and the transcript is inserted into the frontmost app in real time, powered by local ASR models
- VAD-based realtime recognition with hold-to-talk (Shift) and push-to-talk toggle-key modes
- Select any installed microphone input device; mute/unmute on demand
- LLM polish modes before insertion: raw, light, structured, formal, translate, plus user-defined custom modes
- Hotwords and working-language context to fix misrecognitions
- Built-in zh/en prompts, user-editable with restore-to-defaults
- OpenAI-compatible providers with per-provider thinking disable and opt-in proxy

### Screenshot & OCR
- Capture screen regions with a global shortcut
- Text recognition with configurable detection and recognition models
- Document layout analysis, orientation detection, formula recognition, and table extraction
- End-to-end document OCR with the Ovis OCR2 model (markdown-direct output, selectable variants)
- Markdown-direct viewer: visual-region overlay, raw markdown rendering (math, code, tables), whole-page translation, copy as markdown
- Overlay results with bounding boxes

### Snap Translate
- One-shortcut workflow: screenshot → OCR → translation
- Supports 38+ target languages

### Translation
- Built-in local translation engine
- Translate live captions in real-time
- Configurable target language
- Auto-start translation service on boot

### Subtitle Editor
- Create and manage subtitle projects
- Multi-track support
- Waveform visualization with video player preview
- Full segment editing: add, split, merge, reorder, bulk shift
- Undo/redo
- Re-transcribe individual segments

### Model Management
- Browse, download, and delete AI models from built-in catalog
- Model groups: ASR, OCR, translation, VAD
- Download progress tracking
- Source preference (HuggingFace / ModelScope)

### Task Monitor
- View all processing tasks with status, progress, and artifacts
- Filter by status (queued / running / done / failed / cancelled)
- Cancel, retry, or delete tasks

### HTTP Server
- REST API for speech transcription and translation
- Configurable host, port, and default model parameters

### Customization
- Configurable global shortcuts (screenshot, snap translate, audio capture)
- Proxy settings
- External tool paths (yt-dlp, ffmpeg, deno)
- OCR model and language preferences
- UI language selection (i18n)
- Light/dark theme

## System Requirements

- macOS Apple Silicon

## Installation

Download the latest release from the [Releases](https://github.com/jason-ni/tf-assistant-app/releases) page.

## Getting Started

1. Launch the application
2. Follow the initial setup wizard to configure your data directory and tool paths
3. Download the desired models from Settings → Models
4. Start transcribing, capturing, or translating

