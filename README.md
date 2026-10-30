# NeuroLab

A computational neuroscience workbench that runs entirely in the browser — including on an iPad — with no server, no account and no paid services. Build and perturb a spiking brain, introduce disease mechanisms, run cognitive tasks, record virtual EEG, and test hypotheses with proper statistics.

> Everything NeuroLab produces comes from simplified computational models. Outputs are properties of those models under their stated assumptions. They are **not** clinical predictions about any real person.

This is the **version 1–6 prototype**: functional neural simulation, learning & memory, Alzheimer's laboratory, electrophysiology, cognitive experimentation, and expanded disease modelling (Parkinson's, epilepsy, multiple sclerosis, traumatic brain injury). Versions 7 (computational psychiatry) and 8 (advanced research environment) plug into the same interfaces.

## Run it

**Hosted (GitHub Pages).** The `dist/` folder is the whole app: one self-contained `index.html` (≈240 KB) plus a web-app manifest.

1. Create a repository (e.g. `neurolab`) under `sofiyatuss`.
2. Upload the contents of `dist/` to the repository root (on iPad: GitHub website → *Add file → Upload files*).
3. *Settings → Pages → Deploy from a branch → main / root*.
4. Open `https://sofiyatuss.github.io/neurolab/`. In Safari, *Share → Add to Home Screen* gives it its own icon.

The app works offline once loaded; the only network request is for the three Google Fonts, with system fonts as fallback.

**From source** (any computer with Node 18+):

```bash
npm install --no-save typescript tsx esbuild   # tooling only; no runtime dependencies
npm test          # 43 automated tests (≈25 s)
npm run typecheck
npm run build     # writes dist/index.html
```

`scripts/build.mjs` imports esbuild from a fixed path used in the build environment; change the import to `'esbuild'` after installing it locally.

## What's inside

| Section | What you can do |
|---|---|
| Brain Explorer | Rotate/zoom a 3D brain (anatomical, network and functional modes) driven by the live simulation; inspect any region; stimulate it; live dashboard; rewind to snapshots; overlay saved runs |
| Neural Network | One microcircuit at cellular resolution: choose LIF / Izhikevich / AdEx / Hodgkin–Huxley, edit synapses and neuromodulators, toggle STDP, homeostasis, pruning, rewiring; raster, membrane traces, f–I curves, weight histograms, learning demo |
| Disease Lab | Alzheimer's (5 separable mechanisms, cascade timeline, stage comparisons, biomarkers), Parkinson's (basal-ganglia selection, STN beta loop), epilepsy (emergent seizure-like events and propagation), MS (conduction delay/failure), TBI (lesion + plasticity-driven recovery) |
| Cognition Lab | 24 tasks across memory, attention, decision-making, perception and emotion, each with an explicit neural→behaviour mapping |
| EEG Lab | Forward model from synaptic currents to 19 scalp electrodes; oddball, semantic-expectancy and error-monitoring paradigms; ERPs, topographies, PSD, spectrograms, coherence; CSV + MNE-Python export |
| Experiment Studio | Hypothesis, IV/DV, control, between/within designs, simulated participants with individual differences, seeds, Welch/paired t, ANOVA, regression, effect sizes, CIs, reproducibility metadata, 9 templates |
| Intervention Lab | Music-and-memory module with switchable assumptions; cholinergic, dopaminergic, GABAergic and glutamatergic interventions |
| Data Analysis | Re-analyse any run, see every simulated participant, Holm-corrected comparisons, overlay runs, analyse pasted data |
| Research Library | Equations, parameters, evidence status, limitations, references, and live validation checks |
| Project Archive | Save/load brains (recipe + exact state), experiments and datasets; export/import JSON between devices |

## Architecture

```
src/
  core/        neuron models, sparse spiking network, neuromodulators, regions, probes   ← simulation engine
  memory/      hippocampal complementary-learning-systems model
  disease/     one module per condition + shared interface (types.ts)                    ← disease mechanisms
  tasks/       24 cognitive task models + registry                                       ← cognitive models
  experiment/  IV registry, paradigms, participant orchestration, music module            ← orchestration
  stats/       t-tests, ANOVA, regression, CIs, Holm                                     ← statistics
  eeg/         FFT/Welch/filters/coherence; forward model, paradigms, ERP analysis
  ui/          app shell, canvas charts, 3D renderer, ten views                          ← visualisation
  storage/     archive (localStorage + JSON export/import)                               ← persistence
  docs/        validation checks shared by the tests and the Research Library
tests/         node:test suites
```

Extending: a new disease implements `DiseaseInfo` plus an `apply…(net, params)` function and registers IVs in `experiment/experiment.ts`; a new task implements `TaskDef` and is added to `tasks/index.ts`, after which it automatically appears in Cognition Lab, Experiment Studio and the Research Library.

## Scientific notes and known limitations

- Region networks are functional abstractions (40–200 E and 10–50 I cells per region), not anatomical reconstructions.
- The default network shows strong beta/gamma population rhythms; there is no thalamocortical alpha generator, so EEG spectra are not human-like at rest. ERP analysis therefore defaults to a 12 Hz low-pass.
- The EEG forward model uses an infinite homogeneous conductor and one radial dipole orientation per region; N400- and ERN-like effects appear as positivities for this reason (documented in the app).
- In the memory model, spaced training does not outperform massed training at the default settings. This is reported as found rather than tuned away.
- Epilepsy has no ion-concentration dynamics, so seizure-like events do not self-terminate realistically.
- The Python/FastAPI/Brian2 backend described in the original brief is not included; this edition implements the same separation of concerns in TypeScript so it runs on an iPad. MNE-Python compatibility is via the exported CSV and loader script.

## Licence

MIT.
