# Adaptive-Ai-optimiser-Kernelsu-Magisk-module
# Optimized Performance Init

**Adaptive performance module for Snapdragon 888 (Xiaomi Devices)**

A compact, safe, and adaptive init script that applies tuned `resetprop` settings for CPU, GPU, memory, 5G and audio — and adapts over time using a lightweight online-learning agent. Designed to be idempotent, minimally invasive, and reversible.

---

## Key features

* Automatic profile selection: `performance`, `balanced`, `battery`, `game`.
* Lightweight Python online-learning agent (optional) that updates a small model on-device to adapt to your usage and thermal behavior.
* Foreground-app-awareness and thermal trip protection to avoid overheating.
* Periodic daemon mode or single-run execution at boot.
* Safe `safe_resetprop` helper to avoid redundant writes, with detailed logging at `/data/local/tmp/optimization.log`.
* Minimal artifacts: model, history and helper scripts live under `/data/local/tmp`.

## Quick install

1. Place the module in your KernelSU Magisk/module folder or appropriate module loader.
2. Ensure the module has executable permissions: `chmod -R 755 <module_folder>`.
3. Reboot — the script runs once at boot by default. To run as daemon use: `sh init.sh daemon`.

## Usage

* Single run (default): `sh init.sh`
* Daemon mode (periodic): `sh init.sh daemon` (interval controlled by `persist.vendor.optim.interval` or `INTERVAL` env)
* Check logs: `/data/local/tmp/optimization.log`

## Files & artifacts

* `init.sh` — main script (boot entry)
* `/data/local/tmp/ai_agent.py` — Python agent (written if python3 is available)
* `/data/local/tmp/perf_model.json` — persisted model
* `/data/local/tmp/perf_history.json` — short history buffer
* `/data/local/tmp/ai_profile_apply.sh` — auto-generated profile applier

## Notes & safety

* Requires root. Use at your own risk.
* The AI agent is conservative by design — it updates only small, local weights and can be disabled by removing the Python binary or the agent file.
* Conflicts: avoids changing thermal-control modules but may overlap with other modules that modify refresh-rate, DFPS, or the same `persist.*` props. Uninstall other refresh-rate/dfps modules first.

## License

MIT — feel free to fork and improve. Pull requests welcome.

---

*Created for Xiaomi 11X Pro (Snapdragon 888). Customize profiles and props in the script to suit other devices.*

