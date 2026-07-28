# tf-assistant

**tf-assistant** is a macOS desktop application that brings together speech transcription, screenshot OCR, translation, and subtitle editing — powered by local AI models.

## Features

### Speech-to-Text
- Transcribe audio files (WAV, MP3, FLAC, M4A, AAC, and more)
- Download and transcribe YouTube videos
- Real-time system capture with live captions
- Speaker diarization (identify who spoke when)
- Multiple transcription modes: voice activity detection, fixed windows, or full diarization
- Export transcripts as SRT

### Screenshot & OCR
- Capture screen regions with a global shortcut
- Text recognition with configurable detection and recognition models
- Document layout analysis, orientation detection, formula recognition, and table extraction
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

Download the latest release from the [Releases](https://github.com/anomalyco/tf-assistant/releases) page.

## Getting Started

1. Launch the application
2. Follow the initial setup wizard to configure your data directory and tool paths
3. Download the desired models from Settings → Models
4. Start transcribing, capturing, or translating

