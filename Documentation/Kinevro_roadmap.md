# Kinevro — Thesis Roadmap (Electron + React + TypeScript)

## Project direction

Kinevro is a webcam-based hand-gesture-controlled generative visual environment. The thesis investigates how static-pose and temporal gesture classification compare when used to control a real-time visual instrument.

**Primary research question:** How do static-pose and temporal gesture classifiers compare in reliability and responsiveness when controlling a real-time generative visual environment?

The implementation should support measurable evaluation, not just a visually impressive demo. Track at least false activations, missed activations, response latency, and task-level usability.

## Recommended technology stack

- **Desktop shell:** Electron
- **Frontend/build:** React, TypeScript, Vite
- **UI styling:** CSS
- **Generative visuals:** Canvas 2D first; consider Three.js/WebGL only when the visual requirements justify it
- **Hand tracking:** Browser-based MediaPipe hand landmarks as the first implementation path
- **ML experiments:** Python, NumPy, pandas, scikit-learn; consider PyTorch if the temporal model needs it
- **Research notebooks:** Jupyter or Google Colab
- **Tests:** Vitest and React Testing Library for frontend logic; Python test tooling for ML code
- **Version control:** Git and GitHub

Keep Electron's main process, preload bridge, React renderer, vision code, and research code clearly separated. Do not expose unrestricted Node.js APIs to the renderer. Use `contextIsolation: true`, `nodeIntegration: false`, and a small, explicitly defined preload API.

## Suggested repository structure

```text
Kinevro/
├── desktop/                 # Electron main process and preload
│   ├── main.ts
│   └── preload.ts
├── src/                     # React renderer
│   ├── app/
│   ├── components/
│   ├── features/
│   │   ├── visuals/
│   │   └── gestures/
│   ├── styles/
│   └── main.tsx
├── public/
├── vision/                  # Tracking experiments or bridge code
├── ml/                      # Dataset preparation and classifier experiments
├── research/                # Protocol, notes, analysis, thesis material
├── protocol/
├── tests/
├── artifacts/               # Exported evaluation outputs; avoid committing large raw data
├── docs/
├── scripts/
├── package.json
├── vite.config.ts
├── tsconfig.json
├── .gitignore
└── README.md
```

This is a target structure, not a requirement to create every directory immediately. Add folders when their responsibilities become real.

## 16-week plan

### Phase 1 — Project foundation (Week 1)
- Confirm the research question, hypotheses, measurable outcomes, and scope.
- Replace the old WPF implementation path with an Electron + Vite + React + TypeScript starter.
- Confirm the app can run in development and open as a desktop window.
- Add a basic automated test and document the setup commands.
- Record versions and major architecture decisions.

**Deliverable:** a minimal Electron desktop app with a working React UI.

### Phase 2 — UI and interaction model (Weeks 2–3)
- Build the main workspace: visual canvas, gesture status, camera status, and settings.
- Define a small set of gestures and the actions each gesture controls.
- Separate UI state from classifier output.
- Add clear permission/error states for camera access.

**Deliverable:** usable interface with simulated gesture events.

### Phase 3 — Generative rendering (Weeks 4–5)
- Implement one reliable Canvas 2D visual system (for example, particles, trails, or flowing shapes).
- Map a few abstract control values to visual parameters.
- Measure frame rate and avoid expensive per-frame React state updates.
- Add a predictable reset state so experiments can start consistently.

**Deliverable:** responsive visual instrument controlled by simulated inputs.

### Phase 4 — Webcam and landmarks (Weeks 6–7)
- Add webcam permission and live camera preview.
- Integrate hand-landmark detection in the renderer using a browser-compatible library.
- Keep camera frames and landmark updates out of React's high-frequency render path where possible.
- Define a stable boundary between landmark extraction and gesture classification.
- Test different lighting, hand positions, backgrounds, and camera distances.

**Deliverable:** real-time hand landmarks with basic diagnostics.

### Phase 5 — Static baseline and pilot (Weeks 8–9)
- Implement a rule-based or static-pose baseline first.
- Add smoothing, confidence thresholds, and a short activation cooldown where appropriate.
- Run a small pilot and refine the protocol before collecting final data.
- Log timestamps, predictions, confidence, activations, and ground-truth labels in a consistent format.

**Deliverable:** baseline classifier and pilot results.

### Phase 6 — Temporal classification (Weeks 10–11)
- Define a fixed temporal window and sequence representation for landmark data.
- Establish a simple temporal baseline before trying a more complex model.
- Compare a temporal model against the static baseline using the same data splits and evaluation conditions.
- Prevent leakage between train/test splits, especially when multiple samples come from the same participant or recording session.
- Report per-class metrics and confusion matrices, not accuracy alone.

**Deliverable:** reproducible static-versus-temporal comparison.

### Phase 7 — Desktop integration and reliability (Week 12)
- Connect classifier outputs to a narrow gesture-event interface.
- Use Electron IPC only for capabilities that genuinely need the main process.
- Add graceful handling for camera denial, device loss, classifier failure, and app restart.
- Test packaged desktop builds, not just development mode.

**Deliverable:** integrated desktop prototype.

### Phase 8 — Evaluation (Weeks 13–14)
- Run the planned study using a documented protocol and consistent tasks.
- Measure false activations, missed activations, response latency, and frame rate.
- Record participant/session conditions and any exclusions.
- Analyze results with appropriate descriptive statistics and uncertainty; avoid claims unsupported by the sample size.
- Keep raw data, derived data, and analysis scripts clearly distinguished.

**Deliverable:** evaluation dataset and reproducible analysis.

### Phase 9 — Thesis and release (Weeks 15–16)
- Write methods, implementation, results, limitations, and future work.
- Explain the trade-offs between static and temporal approaches.
- Include the study protocol, metrics, model details, and reproducibility instructions.
- Prepare a stable demo, installation instructions, and a clean repository.

**Deliverable:** thesis draft/final submission and reproducible Kinevro release.

## Research design essentials

### Candidate hypotheses
- Temporal classification may reduce false activations for gestures defined by motion.
- Static-pose classification may have lower computational and response overhead for gestures that can be recognized from a single frame.
- The practical result depends on gesture vocabulary, recording conditions, model design, and the activation policy.

Treat these as hypotheses to test, not as conclusions.

### Suggested metrics
- **False activations:** unintended control events per minute or per task.
- **Missed activations:** proportion of intended gestures that fail to trigger.
- **Response latency:** elapsed time from the defined gesture onset to the accepted control event.
- **Frame rate:** rendering performance during interaction.
- **Usability:** a short, consistent questionnaire and/or task completion measures.

Define event onset and the timing measurement procedure before the final study.

## Implementation principles

1. **Build the smallest end-to-end loop first:** camera → landmarks → classifier → gesture event → visual change.
2. **Keep rendering independent from classification.** The visual engine should accept abstract control events rather than know model internals.
3. **Use Electron as the shell, not as the whole architecture.** React runs in the renderer; privileged desktop capabilities stay behind preload and IPC.
4. **Avoid a separate Python runtime until needed.** Start with browser-compatible hand tracking and lightweight classification. Introduce a Python worker/service only if model requirements or libraries make it worthwhile.
5. **Optimize after measuring.** Use Canvas 2D first; move to Three.js/WebGL when the desired visual complexity calls for it.
6. **Make experiments reproducible.** Version the protocol, document data splits, and keep evaluation scripts with the project.
7. **Protect privacy.** Make camera state visible, request permission explicitly, and do not save video by default.

## Development milestones

- [ ] Electron + Vite + React + TypeScript launches locally
- [ ] Renderer and main process are separated safely
- [ ] Visual canvas responds to simulated controls
- [ ] Webcam preview and hand landmarks work
- [ ] Static baseline is logged and evaluated
- [ ] Temporal baseline/model is logged and evaluated
- [ ] Evaluation protocol is finalized before final data collection
- [ ] Metrics can be reproduced from saved logs
- [ ] Packaged desktop build is tested on the target machine
- [ ] Thesis documents limitations and reproducibility details
