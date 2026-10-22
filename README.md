# Kinevro

Kinevro is a generative visual environment controlled by webcam-based hand gestures, exploring real-time gesture recognition, interactive graphics, and the reliability of static versus temporal gesture classification.

## Project status

The planned application stack is **Electron + React + TypeScript + Vite**. Electron provides the desktop shell; React and TypeScript power the interface; Vite provides the development/build workflow. The initial visual engine will use Canvas 2D, with Three.js/WebGL considered if required by the intended visuals.

## Research focus

The thesis compares static-pose and temporal gesture classification for controlling a real-time visual environment. Evaluation should consider false activations, missed activations, response latency, and usability rather than accuracy alone.

## Planned architecture

- `desktop/` — Electron main process and preload bridge
- `src/` — React renderer, UI, gesture interaction, and generative visuals
- `vision/` — hand-tracking experiments and integration code
- `ml/` — dataset preparation, training, and evaluation
- `research/` and `protocol/` — research notes, study protocol, and analysis
- `tests/` — frontend and model-related tests
- `docs/` — project and architecture documentation

These directories can be introduced incrementally; do not create empty folders just to match the diagram.

## Security baseline

The Electron renderer should not have unrestricted Node.js access. Keep `nodeIntegration` disabled, enable `contextIsolation`, and expose only the minimum required API through a preload script and `contextBridge`. Validate IPC messages and avoid loading untrusted remote content into a privileged window.

## Development plan

See [`Kinevro_Thesis_Roadmap_Electron.md`](Kinevro_Thesis_Roadmap_Electron.md) for the 16-week plan, research metrics, milestones, and implementation principles.

## Planned development workflow

Once the starter project is created, the usual workflow will be:

```bash
npm install
npm run dev
```

The exact scripts depend on the selected Electron/Vite starter template. Do not assume these commands are available until `package.json` has been created and checked.

## Research integrity

Treat static-versus-temporal performance as an empirical question. Use consistent data splits and evaluation conditions, avoid leakage between participants or recording sessions, and document limitations and deviations from the study protocol.
