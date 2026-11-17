# SRL Using LMS Data (early snapshot)

An early version of the **SRL Analytics Platform**: a React prototype of a learning management system (LMS) that logs student activity (video play, pause, rewind, quizzes, polls, tab focus, scroll, idle) and turns it into **Self-Regulated Learning (SRL)** scores and dashboards for teachers.

> **Looking for the working app? Use [`geethatigulla/srl`](https://github.com/geethatigulla/srl).**
> This repo is a flat upload of an older copy of that project. All source files were put in the repo root, mixed with files from `node_modules` and Vite's dependency cache. It **does not run as-is** (see [Repository status](#repository-status)).

## How this repo differs from `srl`

| | `srl-using-lms-data` (this repo) | [`srl`](https://github.com/geethatigulla/srl) |
|---|---|---|
| Layout | Flat. All `.jsx` files sit in the root, so the `./context/...` and `./pages/...` imports in `App.jsx` do not resolve | Standard Vite layout (`src/context`, `src/pages/...`) |
| Runnable | No. `package.json` is the one from `@eslint/plugin-kit` | Yes: `npm install && npm run dev` |
| Pages | Only one `Dashboard.jsx` (the **teacher** one). The student dashboard was lost because both files share a name | Both dashboards |
| Telemetry | Metadata spread onto the event object, no course or chapter IDs, no click tracking | Adds `course_id`, `chapter_id`, `event_metadata`, mouse-click tracking |
| Video analytics | Play, pause, rewind, skip-attempt, complete, quiz and poll events | Adds pause count, per-segment watch heatmap, `confusion_spot` and `video_dropout` events |
| Teacher tools | Behavior page uses hard-coded clusters and risk alerts. Intervention form only shows a brief confirmation and stores nothing | Clusters worked out from events. Interventions are stored and shown on the student dashboard |
| Courses | Subject list hard-coded in `LearningProgress.jsx` | Subjects served from the mock backend |

## What the app does (this snapshot)

- **Auth**: login, plus teacher and student sign-up with a class join code. Users are held in memory.
- **Student pages**: Learning Progress (chapters, locked content), Video Player (instrumented sample video with an in-video quiz at 45% and a poll at 75%), Leaderboard, AI Report.
- **Teacher pages**: Dashboard, Class Analytics, Live Monitor (polls recent events every 2 s and counts students online, watching, quizzing and idle over the last 60 s), Video Analytics, SRL/Advanced Analytics, Behavior Analytics, Student Profile, Course Management.
- **SRL scoring** (`MockBackendContext.jsx` → `computeStudentSRL`): planning, monitoring, control, reflection and motivation scores (0–100) from simple event-count rules: planning from `chapter_preview`, monitoring from `quiz_retry`, control from `video_rewind`, reflection from `poll_answer`, motivation from total event volume. In this snapshot nothing emits `chapter_preview` or `quiz_retry`, so those two scores stay at their base values. `srl` reworked these rules.
- **Passive telemetry** (`TelemetryContext.jsx`): tab focus and blur, throttled scroll depth, and an `idle_state` event after 60 s of no input.

As in `srl`, there is **no backend and no real LMS log import**. The "LMS data" is events made inside the browser session and kept in React state.

**Tech:** React 19, Vite 7, React Router 7, Recharts, lucide-react.

## Repository status

What is in the root:

- **App source**: `App.jsx`, `main.jsx`, `index.html`, `index.css`, `App.css`, `vite.config.js`, `eslint.config.js`, the `*Context.jsx` files, and one `.jsx` file per page or component.
- **Not project code, can be removed**: about 700 files copied from `node_modules`. These include Babel helpers (`*.js` / `*.js.map`), Vite pre-bundled deps (`_metadata.json`, `chunk-*.js`, `react*.js`, `lucide-react.js`), npm CLI shims (`vite`, `vite.cmd`, `eslint.ps1`, …), `esbuild.exe` (about 11 MB), `.package-lock.json`, `CHANGELOG.md`, `globals.json`, and similar. The repo is about 26 MB in total.
- **`package.json`, `LICENSE` / `license`, `README.md` / `readme.md`** all came from third-party packages (`@eslint/plugin-kit`, `globals`, …). They do **not** describe this project. `README.md` and `readme.md`, and `LICENSE` and `license`, are tracked as separate files that differ only in case. They clash on case-insensitive file systems (Windows, macOS).

### Running it anyway

The simplest way is to clone [`srl`](https://github.com/geethatigulla/srl) instead. To revive this snapshot by hand:

1. Create a Vite React project, or copy `package.json` from `srl`.
2. Move the app files into the layout that `App.jsx` imports: `src/main.jsx`, `src/App.jsx`, `src/context/*Context.jsx`, `src/components/{Layout,Sidebar,TopNav}.jsx`, `src/pages/auth/*`, `src/pages/student/*`, `src/pages/teacher/*`.
3. Recreate the missing student `Dashboard.jsx`. You can take it from `srl`.
4. `npm install && npm run dev`.

Demo accounts are seeded in `MockBackendContext.jsx` (student and teacher, local test values only).

## License

The project has no license of its own. The `LICENSE` and `license` files in this repo belong to bundled third-party packages.
