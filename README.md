# ⚡ ZipForge

> Transform text-based folder structures into fully generated project ZIP files — instantly.

ZipForge is a **client-side, zero-dependency** developer utility that takes a tree-format folder structure, parses it, optionally generates real file contents using a **local Ollama AI model**, and packages everything into a downloadable `.zip` file. No server. No uploads. Runs entirely in your browser.

![Version](https://img.shields.io/badge/version-1.2.0-7fff6e?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-4fc3f7?style=flat-square)
![Ollama](https://img.shields.io/badge/AI-Ollama%20%2B%20qwen2.5--coder-ff6b9d?style=flat-square)

---

## 📸 Features

| Feature | Description |
|---|---|
| 🌳 **Tree Parser** | Parses `├──`, `└──`, `│` tree symbols, indentation, inline `#` comments |
| 📦 **ZIP Generation** | Client-side DEFLATE compression via JSZip — no server needed |
| 🤖 **Ollama AI** | Generates real boilerplate using `qwen2.5-coder:0.5b` locally |
| ⚡ **GPU Detection** | Auto-detects CUDA/GPU via Ollama API and offloads layers |
| 🔄 **Parallel Generation** | 3 files generated simultaneously for faster output |
| 📝 **File Templates** | Static boilerplate for 20+ file types (Minimal / Documented / Full) |
| 👁 **Live Preview** | Parsed tree updates in real-time as you type |
| 🎨 **Dark UI** | Monospace terminal aesthetic, zero external CSS frameworks |

---

## 🖥 System Requirements

### Basic Mode (no AI)
- Any modern browser (Chrome, Firefox, Edge, Safari)
- Internet connection (for Google Fonts + JSZip CDN on first load)
- Python 3.x (for local HTTP server) **or** any static file server

### AI Mode (Ollama integration)
- [Ollama](https://ollama.com) installed and running
- `qwen2.5-coder:0.5b` model pulled
- **For GPU acceleration:** NVIDIA GPU with CUDA compute 6.0+ and CUDA drivers installed

#### Tested Hardware
| Hardware | AI Mode | GPU Offload |
|---|---|---|
| Any CPU | ✅ Works | ❌ N/A |
| NVIDIA MX450 (2GB VRAM) | ✅ Works | ✅ Full GPU |
| NVIDIA GTX 1060+ | ✅ Works | ✅ Full GPU |
| NVIDIA GT 730 (Kepler) | ✅ Works (CPU fallback) | ❌ Too old (compute 3.5) |

---

## 🚀 Quick Start

### Step 1 — Clone or Download

```bash
git clone https://github.com/yourusername/zipforge.git
cd zipforge
```

Or just download `zipforge.html` directly — it's a single self-contained file.

### Step 2 — Serve Locally

> ⚠️ **Do NOT open `zipforge.html` as a `file://` URL.** Browsers block `fetch()` to localhost from file:// pages (CORS). You must serve it over HTTP.

**Python (recommended):**
```bash
# Navigate to the folder containing zipforge.html
cd path/to/zipforge

# Python 3
python -m http.server 8080

# Python 2
python -m SimpleHTTPServer 8080
```

**Node.js:**
```bash
npx serve . -p 8080
```

**VS Code:**
Install the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension and click "Go Live".

### Step 3 — Open in Browser

```
http://localhost:8080/zipforge.html
```

---

## 🤖 Setting Up Ollama AI (Optional)

### Install Ollama

Download from [https://ollama.com/download](https://ollama.com/download) and install.

### Pull the Required Model

```bash
ollama pull qwen2.5-coder:0.5b
```

This downloads ~400MB. Only needs to be done once.

### Start Ollama with CORS Enabled

ZipForge makes API calls to `http://localhost:11434` from the browser. You **must** set `OLLAMA_ORIGINS` or the browser will block the requests.

**Windows (PowerShell):**
```powershell
$env:OLLAMA_ORIGINS = "*"
ollama serve
```

**Windows (CMD):**
```cmd
set OLLAMA_ORIGINS=*
ollama serve
```

**macOS / Linux:**
```bash
OLLAMA_ORIGINS="*" ollama serve
```

> Run these commands **one at a time**. Do not paste both on the same line.

### Enable GPU Acceleration (NVIDIA only)

Check if your GPU is detected:
```bash
nvidia-smi
```

Check CUDA version — Ollama requires **CUDA 11.3+**:
```bash
nvidia-smi | grep "CUDA Version"
```

Stop any large models that might be occupying VRAM:
```bash
ollama stop <model-name>
ollama ps   # should show empty
```

Set CUDA device explicitly before starting Ollama:

**Windows (PowerShell):**
```powershell
$env:OLLAMA_ORIGINS = "*"
$env:CUDA_VISIBLE_DEVICES = "0"
ollama serve
```

Warm up the model to confirm GPU usage:
```bash
ollama run qwen2.5-coder:0.5b "say hi"
ollama ps
# Should show: PROCESSOR = 100% GPU
```

---

## 📖 How to Use ZipForge

### 1. Paste Your Folder Structure

In the **Structure Input** box, paste a tree-format folder structure:

```
MyProject/
├── src/
│   ├── main.py
│   ├── routes/
│   │   ├── users.py
│   │   └── auth.py
│   └── utils/
│       └── helpers.py
├── tests/
│   └── test_api.py
├── Dockerfile
├── requirements.txt
└── README.md
```

**Supported formats:**
- Standard tree characters: `├──`, `└──`, `│`
- Space/tab indentation
- Inline comments are stripped: `models/  # ML files` → `models/`
- Trailing `/` marks directories; files detected by extension

### 2. Configure Options

| Option | Description |
|---|---|
| **File Templates** | Toggle static boilerplate on/off |
| **Include .gitignore** | Auto-adds a `.gitignore` to the project root |
| **ZIP Name** | Set the output filename (default: `project`) |
| **Template Style** | Minimal / Documented / Full boilerplate |

### 3. Configure Ollama (if using AI)

1. Enter your Ollama URL (default: `http://localhost:11434`)
2. Click **ping** — the dot turns green if connected
3. Check the GPU badge: `⚡ GPU active` or `🖥 CPU only`
4. If the model isn't pulled, you'll see a red warning with the pull command
5. Paste a project description for context-aware generation
6. Toggle **Generate with Ollama** ON

### 4. Generate

Click **Generate Project ZIP**. The status bar shows real-time progress:
- Without AI: instant
- With AI: `Ollama: auth.py ✓ — 3/8 files done` (3 files at a time)

### 5. Download

Click **Download ZIP** once it appears. Open any generated file to verify the content.

---

## 🧩 Input Format Reference

```
ProjectName/           ← root folder (trailing / = directory)
├── src/               ← subdirectory
│   ├── main.py        ← file (has extension)
│   └── utils/         ← nested directory
│       └── helper.py
├── Makefile           ← file without extension (auto-detected)
├── .env.example       ← dotfiles detected as files
└── README.md
```

**Inline comments are supported and stripped:**
```
├── models/    # weights go here   →   models/
```

**All tree styles work:**
```
# Style 1: Standard unicode
├── src/
└── main.py

# Style 2: ASCII fallback
+-- src/
`-- main.py

# Style 3: Plain indentation
  src/
    main.py
```

---

## 📁 Project Structure

```
zipforge/
├── zipforge.html      ← entire application (single file)
├── README.md          ← this file
├── LICENSE            ← MIT license
└── .gitignore
```

ZipForge is intentionally a **single HTML file**. All CSS, JavaScript, and markup are bundled together for maximum portability — drop it anywhere and serve it.

---

## 🔧 Configuration Reference

### Ollama API Options (in code)

| Option | Value | Effect |
|---|---|---|
| `num_gpu` | `99` if GPU detected, `0` otherwise | Offload transformer layers to VRAM |
| `num_thread` | `4` | CPU threads (tuned for quad-core i5) |
| `temperature` | `0.2` | Low = deterministic, faster output |
| `num_predict` | `400` | Max output tokens per file |
| Timeout | `120s` | Per-file generation timeout |
| Concurrency | `3` | Files generated in parallel |

### Changing the Model

The model is locked to `qwen2.5-coder:0.5b` in the UI. To use a different model, open `zipforge.html` in a text editor and find:

```javascript
const model = 'qwen2.5-coder:0.5b'; // locked
```

Change it to any model you have pulled (e.g. `qwen2.5-coder:1.5b`, `gemma3:1b`).

---

## ⚠️ Troubleshooting

### "Ollama unreachable"
- Make sure `ollama serve` is running
- Make sure `OLLAMA_ORIGINS=*` is set **before** running `ollama serve`
- Make sure you're accessing ZipForge via `http://localhost:8080` not `file://`

### Model generates wrong content for `requirements.txt` / `Dockerfile`
- This is a known limitation of small models — they sometimes ignore file type context
- Use **Documented** or **Full** template style without AI for config files

### GPU not being used (`100% CPU` in `ollama ps`)
- Your model may be too large for your VRAM — `qwen2.5-coder:0.5b` needs ~500MB VRAM
- Run `ollama stop <other-model>` to free VRAM
- Verify CUDA compute ≥ 6.0 with `nvidia-smi`

### Timeout / fallback to static template
- Generation fell back silently — increase timeout in code from `120000` to `180000`
- Or use a faster/smaller model

### ZIP has wrong folder names
- Make sure your structure uses supported tree characters
- Inline comments (`# ...`) are auto-stripped
- Trailing spaces in folder names will be trimmed

---

## 🛠 Tech Stack

| Component | Technology |
|---|---|
| UI | Vanilla HTML/CSS/JS — no frameworks |
| Fonts | Syne (display) + Space Mono (mono) via Google Fonts |
| ZIP | [JSZip 3.10.1](https://stuk.github.io/jszip/) via cdnjs |
| AI | [Ollama](https://ollama.com) local inference API |
| Model | [qwen2.5-coder:0.5b](https://ollama.com/library/qwen2.5-coder) |
| GPU | CUDA via Ollama's `num_gpu` option |

---

## 📄 License

MIT © 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software.

---

## 🤝 Contributing

1. Fork the repo
2. Make your changes to `zipforge.html`
3. Test with both AI mode on and off
4. Submit a PR with a description of what you changed and why

**Good first issues:**
- File-type aware prompts for `Dockerfile`, `requirements.txt`, `README.md`
- Streaming output support (show generation token by token)
- Custom model selector (unlock the model field)
- Export parsed tree as JSON

---

*Built with [Ollama](https://ollama.com) · Powered by [qwen2.5-coder](https://ollama.com/library/qwen2.5-coder)*
