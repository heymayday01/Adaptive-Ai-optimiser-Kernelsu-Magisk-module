<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Orbitron&weight=800&size=38&duration=3000&pause=1000&color=58A6FF&center=true&vCenter=true&width=700&height=80&lines=Adaptive+AI+Optimiser" alt="Project Title" />

<h3>🧬 KernelSU / Magisk module with on-device reinforcement-learning CPU/GPU governor for Snapdragon 888 Xiaomi devices</h3>

<p>
  <a href="https://github.com/heymayday01/Adaptive-Ai-optimiser-Kernelsu-Magisk-module/stargazers"><img src="https://img.shields.io/github/stars/heymayday01/Adaptive-Ai-optimiser-Kernelsu-Magisk-module?style=for-the-badge&color=58A6FF&labelColor=0D1117" /></a>
  <img src="https://img.shields.io/github/forks/heymayday01/Adaptive-Ai-optimiser-Kernelsu-Magisk-module?style=for-the-badge&color=FF6B35&labelColor=0D1117" />
  <img src="https://img.shields.io/github/repo-size/heymayday01/Adaptive-Ai-optimiser-Kernelsu-Magisk-module?style=for-the-badge&color=3FB950&labelColor=0D1117" />
  <img src="https://img.shields.io/badge/Platform-Android-3DDC84?style=for-the-badge&logo=android&logoColor=white&labelColor=0D1117" />
  <img src="https://img.shields.io/badge/SoC-Snapdragon%20888-1976D2?style=for-the-badge&logo=qualcomm&logoColor=white&labelColor=0D1117" />
  <img src="https://img.shields.io/badge/Requires-Root-EA4335?style=for-the-badge&logo=android&logoColor=white&labelColor=0D1117" />
</p>

<p>
  <img src="https://img.shields.io/badge/Module-Performance%20Optimizer-FF6B35?style=flat-square" />
  <img src="https://img.shields.io/badge/AI-Agent-Online%20Learning-58A6FF?style=flat-square" />
  <img src="https://img.shields.io/badge/Profiles-perf%20%7C%20balanced%20%7C%20battery%20%7C%20game-3FB950?style=flat-square" />
  <img src="https://img.shields.io/badge/License-MIT-blue?style=flat-square" />
</p>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%" />

</div>

## 🧠 What is this?

**Adaptive AI Optimiser** is a root-level KernelSU / Magisk module that applies tuned `resetprop` settings for CPU, GPU, memory, 5G, and audio on **Snapdragon 888 Xiaomi devices** — and adapts to your usage over time using a lightweight Python reinforcement-learning agent that runs on-device.

It is **compact**, **safe**, **reversible**, and **idempotent**. By design it modifies only `persist.*` properties — it never patches the kernel binary itself.

> **2024 milestone:** 30% battery life gain with 0% performance loss in daily-driver workloads on Mi 11X Pro.

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🎯 **Adaptive profiles** | Auto-selects `performance` / `balanced` / `battery` / `game` based on foreground app + thermal state |
| 🧬 **On-device RL agent** | Python online-learning agent (≈8 KB) updates a small Q-table on-device every INTERVAL |
| 🔥 **Thermal trip protection** | Backs off to `battery` profile when the SoC hits thermal trip points — never burns your hands |
| 🛡️ **Safe `resetprop` writes** | `safe_resetprop` helper deduplicates writes and logs every change to `/data/local/tmp/optimization.log` |
| ⚡ **Foreground-aware** | Detects foreground app and switches profile — gaming → performance, reading → battery |
| 🪶 **Minimal artifacts** | All model + history + helper scripts live under `/data/local/tmp` — no `/system` pollution |
| 🔁 **Reversible** | Remove the module → all changes revert on next boot |

---

## 🏗️ How it works

```
┌─────────────────────────────────────────────────────────────────┐
│                    Boot / Daemon Tick                            │
│                                                                  │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────────┐  │
│  │  init.sh     │ →  │  observe()   │ →  │  ai_agent.py     │  │
│  │  (entry)     │    │  state vec   │    │  Q-update → argmax│  │
│  └──────────────┘    └──────────────┘    └──────────────────┘  │
│         │                  │                     │              │
│         ▼                  ▼                     ▼              │
│   chmod +x           foreground app        pick profile         │
│   setup props        thermal zone          write props          │
│   start daemon       battery %             via safe_resetprop   │
└─────────────────────────────────────────────────────────────────┘
```

**State vector:** `[cpu_load, gpu_load, temp, battery, foreground_app_hash, time_of_day]`
**Action space:** `[{performance}, {balanced}, {battery}, {game}]`
**Reward:** `+battery_saved - perf_penalty - thermal_violation`

---

## 📦 Install

### Prerequisites

- Rooted Xiaomi Snapdragon 888 device (HAYDN / VENUS / STAR etc.)
- KernelSU **or** Magisk installed
- Python 3 (optional — only needed for the AI agent; module runs fine without it as a static optimizer)

### Steps

1. Download the latest `Aryon_Ai_HyperOS.zip` from the [releases page](https://github.com/heymayday01/Adaptive-Ai-optimiser-Kernelsu-Magisk-module/releases) (or use the committed zip in the repo root).
2. Open KernelSU / Magisk → **Modules** → **Install from storage** → select the zip.
3. Reboot. The module runs once at boot by default.

---

## 🎮 Usage

```bash
# Default: single run at boot
sh init.sh

# Daemon mode (periodic, every INTERVAL seconds)
sh init.sh daemon

# Check the live log
tail -f /data/local/tmp/optimization.log

# Force a specific profile
setprop persist.vendor.optim.profile performance

# Disable the AI agent (keep static optimization)
rm /data/local/tmp/ai_agent.py && sh init.sh
```

### Tunable properties

| Prop | Default | Effect |
|------|---------|--------|
| `persist.vendor.optim.profile` | `balanced` | Force a specific profile |
| `persist.vendor.optim.interval` | `30` | Daemon tick interval in seconds |
| `persist.vendor.optim.learning` | `1` | `1` = enable RL agent, `0` = static |
| `persist.vendor.optim.thermal_cap` | `42` | SoC temp °C above which we back off to `battery` |

---

## 📂 Module layout

```
Adaptive-Ai-optimiser-Kernelsu-Magisk-module/
├── Aryon_Ai_HyperOS.zip       ← Flashable module zip
├── LICENSE                    ← MIT
└── README.md                  ← This file

# After install, on-device artifacts:
/data/local/tmp/
├── ai_agent.py                ← Python RL agent (8 KB)
├── perf_model.json            ← Persisted Q-table
├── perf_history.json          ← Short rolling history buffer
└── ai_profile_apply.sh        ← Auto-generated profile applier
```

---

## 🛠️ Tech Stack

<p>
  <img src="https://img.shields.io/badge/Shell-121011?style=for-the-badge&logo=gnu-bash&logoColor=white" />
  <img src="https://img.shields.io/badge/Python_3-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white" />
  <img src="https://img.shields.io/badge/Magisk-00C851?style=for-the-badge&logo=android&logoColor=white" />
  <img src="https://img.shields.io/badge/KernelSU-FF6B35?style=for-the-badge&logo=android&logoColor=white" />
</p>

---

## ⚠️ Safety & Compatibility

- **Requires root.** Use at your own risk. The author is not liable for bricked devices.
- The AI agent is **conservative by design** — it updates only small local weights and can be disabled by removing `ai_agent.py` or setting `persist.vendor.optim.learning=0`.
- **Conflicts:** this module does not modify thermal-control modules directly, but it may overlap with other modules that touch refresh-rate / DFPS / `persist.*` props. **Uninstall other refresh-rate / DFPS modules first.**
- Tested on: Mi 11X Pro (HAYDN), Mi 11 (VENUS), Mi 11 Ultra (STAR). Other SD888 Xiaomi devices should work but are untested.

---

## 📜 License

MIT — fork it, improve it, send a PR. Pull requests welcome.

---

## 👤 Author

<div align="center">

**Aryan Thakare** — [@heymayday01](https://github.com/heymayday01)

[![GitHub](https://img.shields.io/badge/GitHub-heymayday01-181717?style=for-the-badge&logo=github)](https://github.com/heymayday01)

<sub>⭐ If your battery lasted longer today because of this — give it a star.</sub>

</div>
