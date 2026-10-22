# Kinevro — Thesis Roadmap (Electron + React + TypeScript + Python)

## 1. Project direction

Kinevro is a webcam-based hand-gesture-controlled generative visual environment. The master's thesis compares static-pose and temporal gesture classification for reliable real-time control.

**Research question:** How do static-pose and temporal gesture classifiers compare in reliability and responsiveness when controlling a real-time generative visual environment?

The project should evaluate false activations, missed activations, response latency, frame rate, and usability. These are research questions to measure, not outcomes to assume.

## 2. Final planned stack

| Layer | Technology | Responsibility |
|---|---|---|
| Desktop shell | Electron | Application window, lifecycle, packaging |
| UI | React + TypeScript + CSS | Controls, status, settings, feedback |
| Frontend build | Vite | Development server and frontend build |
| Generative rendering | Canvas 2D first; Three.js/WebGL if needed | Real-time visual environment |
| Initial hand tracking | MediaPipe in the browser/renderer | Webcam access and hand-landmark extraction |
| Runtime ML backend | Python | Gesture classification and ML-related processing |
| ML/data libraries | NumPy, pandas, scikit-learn; PyTorch if needed | Feature processing, baselines, temporal model experiments |
| Research | Jupyter / Google Colab | Offline data exploration, training, and evaluation |
| Version control | Git + GitHub | Source history and reproducibility |
| Tests | Vitest / React Testing Library; Python tests | UI logic, protocol, classifier tests |

**Important distinction:** Electron's main process uses Node.js, and the React renderer uses TypeScript/JavaScript. That is normal for Electron. Python is the dedicated ML backend. This does not require rewriting the UI in Python.

## 3. Runtime architecture

Initial planned data flow:

1. The Electron main process opens the desktop window and manages application lifecycle.
2. The React renderer displays the UI and generative visuals.
3. The renderer requests camera permission and runs browser-compatible MediaPipe hand tracking.
4. The tracking layer produces landmarks or derived features.
5. A narrow, documented interface sends the required feature/sequence data to the Python ML process.
6. Python returns classification results, confidence, and/or gesture events.
7. The frontend applies activation rules and maps accepted events to visual parameters.
8. The UI displays camera, tracking, and classifier status.

Keep the protocol small and versioned. Do not send full video frames to Python unless an experiment shows that Python must own tracking. Prefer sending landmarks or compact feature sequences when that satisfies the research design.

### Python process communication

For the first integrated version, choose one communication method only after the minimal app runs:
- **Standard input/output (stdio):** a simple local worker protocol, often a good starting point for one desktop app and one worker.
- **Loopback-only local API/socket:** useful if independent endpoints or multiple clients are genuinely needed.

Do not add FastAPI, a database, or a message broker without a concrete requirement. Validate messages and handle worker startup, shutdown, timeouts, and crashes.

## 4. Electron security baseline

- Set `contextIsolation: true`.
- Keep `nodeIntegration: false` in the renderer.
- Use a preload script and `contextBridge` to expose only a small, explicit API.
- Validate IPC messages and arguments.
- Do not load untrusted remote content into a privileged window.
- Keep camera state visible and request camera permission explicitly.
- Do not save webcam video by default.
- Keep raw recordings and private datasets out of version control.

## 5. Suggested repository structure

```text
Kinevro/
├── desktop/                 # Electron main process and preload
├── src/                     # React renderer
│   ├── app/
│   ├── components/
│   ├── features/
│   │   ├── visuals/
│   │   └── gestures/
│   ├── styles/
│   └── main.tsx
├── public/
├── python/                  # Runtime ML backend and protocol
├── ml/                      # Training, evaluation, notebooks/scripts
├── vision/                  # Optional tracking experiments; don't duplicate runtime code
├── research/
├── protocol/
├── tests/
├── artifacts/
├── docs/
├── scripts/
├── package.json
├── vite.config.ts
├── tsconfig.json
├── .gitignore
└── README.md
```

Create directories when their responsibilities are needed; this is a target map, not a requirement to create empty folders now. Keep runtime Python code separate from offline training experiments.

## 6. Sixteen-week roadmap

### Week 1 — Foundation
- Confirm scope, research question, hypotheses, metrics, and repo state.
- Create an Electron + Vite + React + TypeScript starter in the existing repository.
- Confirm it opens a desktop window and has a basic test.
- Record dependency versions and architecture decisions.

**Deliverable:** working minimal Electron desktop app.

### Weeks 2–3 — Interface
- Build the workspace, visual canvas, camera status, gesture status, and settings.
- Define a small gesture vocabulary and the controls each gesture affects.
- Implement simulated gesture events before connecting a camera.
- Add explicit camera permission and error states.

**Deliverable:** interface testable without ML.

### Weeks 4–5 — Generative visuals
- Implement one reliable Canvas 2D effect, such as particles, trails, or flowing shapes.
- Map abstract control values to visual parameters.
- Keep high-frequency animation out of React's state-update path.
- Add reset behavior and measure rendering frame rate.

**Deliverable:** responsive visual instrument controlled by simulated inputs.

### Weeks 6–7 — Webcam and landmarks
- Add webcam access and a live preview.
- Integrate browser-compatible MediaPipe hand tracking.
- Show landmarks and tracking confidence/status.
- Test lighting, background, distance, and hand position.
- Keep tracking output separate from the classifier interface.

**Deliverable:** real-time landmarks and diagnostics.

### Weeks 8–9 — Static baseline and pilot
- Implement a rule-based or static-pose baseline in Python.
- Define thresholds, smoothing, cooldowns, and neutral/reset behavior.
- Run a small pilot and refine the study protocol.
- Log timestamps, predictions, confidence, accepted events, and labels.

**Deliverable:** reproducible baseline and pilot findings.

### Weeks 10–11 — Temporal classification
- Define sequence windows and features.
- Establish a simple temporal baseline before using a complex model.
- Train/evaluate with participant- or session-aware splits to avoid leakage.
- Compare static and temporal methods on equivalent data and conditions.
- Report per-class metrics and confusion matrices, not accuracy alone.

**Deliverable:** reproducible static-versus-temporal comparison.

### Week 12 — Integration and reliability
- Connect the frontend to the Python worker through the selected protocol.
- Add startup/shutdown, timeouts, worker failure handling, and clear status.
- Test camera loss and classifier failure.
- Test a packaged Electron build.

**Deliverable:** integrated desktop prototype.

### Weeks 13–14 — Evaluation
- Freeze the protocol before final data collection.
- Measure false activations, missed activations, response latency, frame rate, and usability.
- Record conditions and exclusions.
- Analyze results with appropriate statistics and uncertainty.
- Keep raw, derived, and analysis data distinct.

**Deliverable:** evaluation dataset and reproducible analysis.

### Weeks 15–16 — Thesis and release
- Write methods, implementation, results, limitations, and future work.
- Explain the trade-offs found between static and temporal classification.
- Document the protocol, model, data splits, and reproducibility steps.
- Prepare a stable demo, setup guide, and repository.

**Deliverable:** thesis and reproducible Kinevro release.

## 7. Research design

### Candidate hypotheses
- Temporal classification may reduce false activations for motion-defined gestures.
- Static-pose classification may have lower inference overhead for gestures recognizable from one frame.
- The outcome depends on gesture vocabulary, environment, model, and activation policy.

### Suggested metrics
- **False activations:** unintended events per minute or task.
- **Missed activations:** proportion of intended gestures that fail to trigger.
- **Response latency:** time from defined gesture onset to accepted event.
- **End-to-end latency:** time from gesture onset to visible effect.
- **Frame rate:** rendering performance under interaction.
- **Usability:** a consistent questionnaire and/or task completion metrics.

Define onset, event acceptance, and timing procedures before final collection.

## 8. Implementation principles

1. Build the smallest end-to-end loop first: simulated event → visual change.
2. Add the webcam and tracking before integrating the Python classifier.
3. Keep rendering independent of model internals.
4. Keep the Python runtime backend separate from offline training scripts.
5. Start with Canvas 2D; use Three.js/WebGL if the target visuals require it.
6. Measure latency and reliability instead of assuming a model is better.
7. Use reproducible splits and version the protocol.
8. Add only the communication infrastructure needed by the first working loop.
9. Do not add Java or .NET as a separate action engine; they are not part of the current planned stack.

## 9. Milestones

- [ ] Electron + Vite + React + TypeScript launches locally
- [ ] Safe preload bridge and renderer separation
- [ ] Canvas responds to simulated controls
- [ ] Webcam preview and hand landmarks work
- [ ] Python classifier runs independently with test inputs
- [ ] Frontend/backend message protocol is documented and tested
- [ ] Static baseline is logged and evaluated
- [ ] Temporal approach is logged and evaluated
- [ ] Evaluation metrics are reproducible
- [ ] Packaged desktop build is tested
- [ ] Thesis reports results and limitations accurately

## 10. Superseded architecture notes

This document replaces older plans that proposed WPF/WebView2/.NET or a Java action engine. Those are not part of the current Kinevro architecture. Keep older files for historical reference if desired, but label them clearly as superseded and do not use them as implementation instructions.
