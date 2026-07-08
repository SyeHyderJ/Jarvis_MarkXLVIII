# ⚙️ JARVIS — Mark XLVIII

### A Real-Time, Cross-Platform Voice AI Assistant Inspired by *Iron Man*'s J.A.R.V.I.S.

**Maintained & extended by [Syed Hyder Jaffari](https://www.linkedin.com/in/syed-hyder-jaffari-344729290/)**
Originally built by [FatihMakes](https://www.youtube.com/@FatihMakes) · [Original repo](https://github.com/FatihMakes/Mark-XLVIII)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://www.linkedin.com/in/syed-hyder-jaffari-344729290/)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey)](https://creativecommons.org/licenses/by-nc/4.0/)
[![Python](https://img.shields.io/badge/Python-3.11%20%7C%203.12-blue)](https://www.python.org/)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-informational)]()

---

## 📌 About This Project

JARVIS (Mark XLVIII) is a real-time voice AI assistant that can **hear, see, understand, and control a computer** — across Windows, macOS, and Linux. It's built on the Gemini Live API for native low-latency audio streaming, giving it natural, interruptible conversation rather than the typical stilted request/response loop.

Named after **J.A.R.V.I.S.**, Tony Stark's AI in *Iron Man*, this project is a hands-on attempt to build that same always-listening, always-useful assistant with real, available technology. It started as an open-source build by **FatihMakes**; I forked it to study the architecture, fix real engineering problems in it, and extend it with my own features going forward. Full attribution and license terms are below.

**Why this project is here:** it's a working example of systems-level engineering — real-time audio streaming, concurrency, cross-platform OS integration, and LLM tool-calling — not a toy demo. I use it as an ongoing sandbox for hardening latency, reliability, and feature scope.

---

## ✅ Project Status & Roadmap

- [x] Real-time voice conversation via Gemini Live API
- [x] Cross-platform system control (Windows / macOS / Linux)
- [x] Screen + webcam visual awareness
- [x] Persistent cross-session memory
- [x] Sub-100ms interrupt handling
- [x] Parallel news/web search with automatic fallback
- [x] OS-native scheduled reminders
- [x] Hardware telemetry (CPU / RAM / GPU / temp) with voice alerts
- [x] Language-aware, non-mixed response formatting
- [ ] Expanded autonomous multi-step agent mode
- [ ] Additional language support beyond Turkish/English
- [ ] Broader OS-level automation hooks
- [ ] Continued latency optimization across voice, vision, and search
- [ ] Public feature-request tracking

*Checklist reflects current build status and is updated as work lands — see commit history for details.*

---

## 🚀 Core Capabilities

| Feature | Description |
|---|---|
| 🎙️ Real-time Voice | Ultra-low latency conversation in any language via Gemini Live API |
| 🖥️ System Control | Launch apps, adjust volume/brightness, WiFi, shortcuts, power — all by voice |
| 🧩 Autonomous Tasks | High-level planning for complex multi-step goals via agent mode |
| 👁️ Visual Awareness | Real-time screen capture and webcam vision piped into the main Gemini session |
| 🧠 Persistent Memory | Remembers projects, preferences, and personal context across sessions |
| ⌨️ Hybrid Input | Seamlessly switch between keyboard typing and voice commands |
| 🌅 Morning Briefing | On first boot: greets you, reads the time, fetches live news, checks weather |
| 🔔 Proactive Check-ins | After 15 minutes of silence, offers something useful — model-driven, not hardcoded |
| 📊 Hardware Monitoring | Continuous CPU/RAM/GPU/temperature telemetry with voice alerts on threshold breach |
| 🌤️ Weather Report | Live, location-personalized weather data |
| 🗺️ Dynamic Content Panel | Scrollable display layer rendering web results, news, and search data with timestamps |
| 🔍 Multi-Mode Web Search | `news` / `research` / `price` / `compare` / `search` — Gemini Grounded first, DDG fallback |
| ⏰ Smart Reminders | OS-native scheduled notifications (Task Scheduler / LaunchAgent / systemd) |
| ✈️ Flight Finder | Live flight price and availability lookup |
| 📂 File Processor | Read, summarize, and answer questions about local files |
| 💻 Code Helper | Inline code review, debugging, and generation |
| 🌐 Browser Control | Open URLs, navigate tabs, interact with the browser by voice |
| 🎬 YouTube Control | Search, play, and control YouTube playback by voice |
| 🖱️ Desktop Control | Taskbar, window management, desktop-level operations |
| 🧑‍💻 Silent Language Memory | Detects spoken language on first use and adapts all future sessions automatically |

---

## 🛠️ Engineering Highlights (Mark XLVIII Changes)

These are the specific engineering problems addressed in this build — included here because they demonstrate concrete debugging and systems-design work, not just feature addition:

- **Sub-100ms interrupt handling** — audio streamed in ~50ms chunks so an interrupt fires within one slice instead of waiting out a full buffer drain (previously up to 4s).
- **Concurrent fallback search** — Gemini Grounded Search and DuckDuckGo news run in parallel daemon threads; first valid result wins, eliminating stall time on upstream errors.
- **Corrected news data source** — switched DDG integration from `.text()` (homepage URLs) to `.news()` (actual article URLs, titles, snippets).
- **Overlapping startup sequence** — briefing audio and background news fetch run concurrently instead of sequentially, removing dead air on boot.
- **Exponential backoff reconnection** — network retry logic (3s → 6s → 12s → 60s capped) replacing a tight retry loop with no backoff.
- **Echo-loop guarding** — cooldown + busy-flag logic prevents the assistant's own voice output from re-triggering vision processing.
- **Session state isolation** — all transient flags reset cleanly on reconnect, fixing a bug where crashed-session state could persist and break subsequent turns.
- **Zero terminal windows** — subprocess patch suppresses all child-process console flashes on Windows.

---

## ⚡ Quick Start

```bash
git clone https://github.com/SyeHyderJ/Jarvis_MarkXLVIII.git
cd Jarvis_MarkXLVIII
pip install -r requirements.txt
python main.py
```

> ⚠️ **Note:** Some OS-specific dependencies aren't bundled in `requirements.txt` to keep the repo lightweight. If you hit a `ModuleNotFoundError`, install the missing package with `pip install <module_name>`.

---

## 📋 Requirements

| Requirement | Details |
| --- | --- |
| **OS** | Windows 10/11, macOS, or Linux |
| **Python** | 3.11 or 3.12 |
| **Microphone** | Required for voice interaction |
| **API Key** | Free Gemini API key (`config/api_keys.json`) |

---

## 🗂️ Project Structure

```
Jarvis_MarkXLVIII/
├── main.py                  # Core loop — Gemini Live session, audio I/O, tool dispatch
├── ui.py                    # PyQt6 HUD — waveform, log panel, interrupt button, camera feed
├── setup.py                 # First-run configuration wizard
├── actions/
│   ├── web_search.py        # Gemini + DDG parallel search (news, research, price, compare)
│   ├── screen_processor.py  # Screen capture & webcam vision via Gemini Live
│   ├── reminder.py          # OS-native scheduled notifications
│   ├── system_monitor.py    # CPU / RAM / GPU / temperature telemetry
│   ├── computer_settings.py # Volume, brightness, WiFi, power
│   ├── computer_control.py  # Keyboard shortcuts, mouse, window management
│   ├── open_app.py          # Application launcher
│   ├── browser_control.py   # Web browser control
│   ├── file_controller.py   # File system operations
│   ├── file_processor.py    # Document reading and summarization
│   ├── send_message.py      # Messaging integration
│   ├── weather_report.py    # Live weather data
│   ├── flight_finder.py     # Flight search
│   ├── youtube_video.py     # YouTube playback control
│   ├── game_updater.py      # Game update management
│   ├── code_helper.py       # Code review and generation
│   ├── dev_agent.py         # Developer task agent
│   ├── desktop.py           # Desktop and taskbar control
│   └── proactive.py         # Proactive silence-break suggestions
├── memory/                  # Persistent key-value memory store
├── core/
│   └── prompt.txt           # JARVIS personality and tool-routing rules
└── config/
    └── api_keys.json        # API key and system configuration (gitignored — not tracked)
```

---

## 🔮 Roadmap

Planned directions for future development:

- Deeper multi-step autonomous task execution (agent mode expansion)
- Expanded visual reasoning over live screen + camera feeds
- Additional language support beyond Turkish/English
- More native OS-level automation hooks
- Continued latency reduction across voice, vision, and search
- Ongoing bug fixes and reliability hardening

---

## ⚠️ License & Attribution

This project is a fork of [**Mark XLVIII**](https://github.com/FatihMakes/Mark-XLVIII) by **FatihMakes**, licensed under **[Creative Commons BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)** — personal, non-commercial use only, with attribution required.

All changes, fixes, and features documented in this fork beyond the original base are my own work, built on top of that foundation.

- Original creator: [FatihMakes on YouTube](https://www.youtube.com/@FatihMakes) · [@fatihmakes on Instagram](https://www.instagram.com/fatihmakes)
- This fork maintained by: **Syed Hyder Jaffari**

---

## 👤 Connect

**Syed Hyder Jaffari**
[LinkedIn](https://www.linkedin.com/in/syed-hyder-jaffari-344729290/)

⭐ If this project is useful or interesting to you, consider starring the repo.