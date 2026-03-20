# Changelog

All notable changes to ZipForge are documented here.

---

## [1.2.0] — 2026-03-21

### Added
- **Ollama GPU detection** — pings `/api/ps` and `/api/version` on connect; sets `num_gpu: 99` to offload layers to VRAM when CUDA is available
- **GPU badge** — shows `⚡ GPU active` or `🖥 CPU only` below the ping button
- **Parallel generation** — 3 files generated simultaneously instead of sequentially
- **Model locked to `qwen2.5-coder:0.5b`** — field is readonly; JS hardcoded; ping warns if model isn't pulled
- **Missing model warning** — ping turns red with `ollama pull` command if qwen isn't installed

### Changed
- Timeout increased from 30s → 120s per file
- Prompt shortened for faster generation on small models
- `num_thread: 4` for i5-class CPUs
- `temperature: 0.2` for deterministic, faster output
- `num_predict: 400` to cap runaway output
- Default model changed from `gemma3:2b` to `qwen2.5-coder:0.5b`

### Fixed
- Trailing backtick bug — now strips all markdown fence variations globally
- Model auto-fill no longer overwrites locked field

---

## [1.1.0] — 2026-03-20

### Added
- **Full Ollama integration** — connect to local Ollama instance, generate file contents with AI
- **Ping button** — test connection to Ollama with live status indicator
- **Project description field** — feeds context into every AI generation prompt
- **"Generate with Ollama" toggle** — AI mode on/off
- **Silent fallback** — if Ollama fails on a file, falls back to static template

### Changed
- AI section replaced old passive description textarea
- Status bar now shows per-file progress during AI generation

---

## [1.0.1] — 2026-03-20

### Fixed
- **Inline comment parsing bug** — folder names like `models/  # Edge LLM files` were
  creating folders with the comment as part of the name. Added `.replace(/\s*#.*$/, '')`
  to `stripTreeChars()`.

---

## [1.0.0] — 2026-03-20

### Initial Release
- Tree structure parser — supports `├──`, `└──`, `│`, space/tab indentation, ASCII fallback
- Client-side ZIP generation via JSZip (DEFLATE compression)
- File templates for 20+ extensions across Minimal / Documented / Full styles
- Live preview with real-time parsing as you type
- Stats panel (folders / files / depth)
- Load example button
- `.gitignore` auto-generation option
- Custom ZIP name
