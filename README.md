# Kinevro

Kinevro is a generative visual environment controlled by webcam-based hand gestures. The master's thesis investigates the reliability and responsiveness of static-pose versus temporal gesture classification for real-time visual control.

## Current planned stack

- **Desktop shell:** Electron
- **Frontend:** React + TypeScript + CSS
- **Build tooling:** Vite
- **Generative visuals:** Canvas 2D first; Three.js/WebGL if justified
- **Initial hand tracking:** MediaPipe in the browser renderer
- **Runtime ML backend:** Python
- **ML/data tools:** NumPy, pandas, scikit-learn; PyTorch if needed
- **Research:** Jupyter / Google Colab
- **Version control:** Git and GitHub

Electron and the frontend use JavaScript/TypeScript because that is Electron's normal application model. Python is the dedicated backend for runtime ML and classifier-related processing. The project does not currently include a separate Java or .NET service.

## Planned data flow

1. Electron opens the desktop window.
2. React renders the UI and generative scene.
3. Browser-compatible MediaPipe tracks the hand and produces landmarks.
4. A small, versioned protocol passes landmarks/features or sequences to Python.
5. Python returns classifier results or gesture events.
6. The frontend applies activation rules and updates the visual scene.

The exact process transport (stdio or a loopback-only API/socket) will be selected when integration begins. Avoid introducing a server framework until there is a concrete need.

## Security

- Use `contextIsolation: true` and `nodeIntegration: false`.
- Expose only a narrow API through Electron's preload script and `contextBridge`.
- Validate IPC and backend messages.
- Keep webcam processing local by default; do not record video by default.
- Do not commit private datasets, recordings, secrets, or large model artifacts.

## Repository map

- `desktop/` — Electron main process and preload
- `src/` — React renderer, UI, and visuals
- `python/` — runtime Python backend and communication protocol
- `ml/` — offline training, notebooks, and evaluation
- `vision/` — optional tracking experiments; avoid duplicating runtime code
- `research/`, `protocol/`, `tests/`, `docs/` — thesis, study protocol, tests, and documentation

Directories can be created incrementally. See [`Kinevro_Thesis_Roadmap_Electron.md`](Kinevro_Thesis_Roadmap_Electron.md) for the roadmap and milestones.

## Development

The exact commands depend on the selected starter template. Once `package.json` exists, inspect its scripts before assuming which commands are available.

## Research integrity

Treat the static-versus-temporal comparison as an empirical question. Use equivalent conditions, avoid participant/session leakage between train and test splits, and report false activations, missed activations, latency, frame rate, and limitations.
