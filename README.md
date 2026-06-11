# RewindAI - Record Once, Understand, Replay Forever

## System Overview

RewindAI is a desktop automation platform that lets you:
- Record your mouse/keyboard actions
- Convert them into structured, editable workflows
- Replay them reliably

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     React + TypeScript UI                    │
│                   (Tauri Webview Frontend)                   │
└────────────────────────────┬────────────────────────────────┘
                             │ Tauri IPC
┌────────────────────────────▼────────────────────────────────┐
│                   Rust Core Backend                          │
│  ┌──────────────────┐  ┌──────────────────┐  ┌─────────────┐ │
│  │  Event Capture   │  │ Workflow Storage │  │   IPC Layer │ │
│  │  (rdev library)  │  │  (SQLite)        │  │             │ │
│  └──────────────────┘  └──────────────────┘  └──────┬──────┘ │
└─────────────────────────────────────────────────────┼────────┘
                                                      │ HTTP
┌─────────────────────────────────────────────────────▼────────┐
│               Python AI Service (FastAPI)                    │
│  ┌──────────────────┐  ┌──────────────────┐                 │
│  │ Event to Action  │  │ Intent Detection │                 │
│  │   Conversion     │  │                  │                 │
│  └──────────────────┘  └──────────────────┘                 │
└──────────────────────────────────────────────────────────────┘
```

## Core Modules

### 1. Event Capture Layer (Rust)
- **Mouse Clicks**: X/Y coordinates + button info
- **Keyboard Input**: Key presses/releases
- **Active Window**: Window title + process name (coming soon)
- **Browser Actions**: URL changes, DOM element interactions (future)

### 2. Abstraction Layer (Python)
- Converts raw events → high-level actions like `click(button: "Login")`
- Handles fallback strategies when UI text is missing

### 3. AI Conversion Layer (Python)
- Removes noise from event stream
- Merges repeated actions
- Detects user intent
- Groups steps into logical tasks (e.g., "Login to GitHub")

### 4. Workflow Representation
- JSON-based workflow format
- Supports:
  - Action types (OpenApp, Navigate, Click, Type, PressKey, Wait, CloseApp)
  - Targets, values, metadata
  - Selectors, fallback strategies
  - Versioning

### 5. Execution Engine (Coming Soon)
- UI element detection (not just fixed coordinates)
- Fallback retries
- Wait handling
- Error recovery
- Browser automation via Playwright
- Desktop automation via Windows UI Automation / PyAutoGUI

### 6. Self-Healing System (Future)
- If old selector fails:
  - Find closest match using text similarity
  - Use visual similarity
  - Fallback to DOM structure

## Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | React + TypeScript |
| Desktop Shell | Tauri 2 |
| Backend Core | Rust |
| AI/Processing | Python (FastAPI) |
| Browser Automation | Playwright |
| Local LLM | Ollama (future) |

## Folder Structure

```
RewindAI/
├── src/                # React + TypeScript frontend
│   ├── types/
│   ├── App.tsx
│   └── ...
├── src-tauri/          # Rust backend
│   ├── src/
│   │   ├── event_capture.rs
│   │   ├── workflow_store.rs
│   │   ├── types.rs
│   │   ├── lib.rs
│   │   └── main.rs
│   └── Cargo.toml
├── python/             # Python AI service
│   ├── src/
│   │   └── main.py
│   └── requirements.txt
└── ...
```

## MVP Roadmap (First 7 Days)

### Day 1-2: Event Capture
- ✅ Basic mouse/keyboard capture
- ⏳ Add active window title + process name

### Day 3-4: UI & Workflow Storage
- ✅ React UI for recording
- ✅ Workflow storage (SQLite)
- ✅ Workflow editing interface

### Day 5-6: AI Conversion
- ✅ Python FastAPI service
- ✅ Event-to-action conversion
- ⏳ Basic intent detection
- ⏳ Noise filtering

### Day 7: Execution
- ⏳ Basic workflow playback
- ⏳ Browser automation (Playwright)

## Critical Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| UI changes break workflows | Self-healing system with multiple selectors |
| High latency in event capture | Rust backend for performance |
| Privacy concerns | Explicit record mode only, no background capture |
| Cross-platform compatibility | Tauri + rdev for cross-OS support |

## Getting Started

### Prerequisites
- Node.js 18+
- Rust 1.75+
- Python 3.10+

### Installation

1. Install dependencies:
```bash
npm install
cd python && pip install -r requirements.txt
```

2. Run the Python service:
```bash
cd python
python src/main.py
```

3. Run the Tauri app:
```bash
npm run tauri dev
```

## Contributing

Contributions welcome! This project is designed to be developer-friendly and extensible.

## License

MIT
