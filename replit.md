# VoltPrep — EE Study Platform

A comprehensive personal Electrical Engineering study web app for GATE, SSC-JE, ESE, Diploma, and B.Tech exam preparation. All data is stored in the browser (localStorage) — no backend required.

## Run & Operate

- `pnpm --filter @workspace/ee-study-app run dev` — run the study app (port 21593, path `/`)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- React + Vite (frontend only, no API calls)
- Tailwind CSS v4 with custom EE-themed palette (dark navy + electric blue)
- Routing: wouter
- Charts: recharts
- Icons: lucide-react
- Fonts: Inter (UI) + JetBrains Mono (formulas/code)
- State: localStorage only via custom hooks

## Where things live

```
artifacts/ee-study-app/src/
  App.tsx                      — router + layout wrapper
  index.css                    — full theme (light + dark mode CSS vars)
  data/
    questions.ts               — 80 MCQ questions across 8 subjects
    formulas.ts                — 48 formulas across 8 subjects
  utils/
    storage.ts                 — localStorage keys, QuizAttempt type, saveAttempt, getAttempts, updateStreak
    quiz.ts                    — shuffleArray, calculateScore, formatTime, filterQuestions, getPerformanceBadge
  hooks/
    useLocalStorage.ts         — generic typed localStorage hook
    useTheme.ts                — dark/light mode toggle + persistence
    useBookmarks.ts            — bookmark add/remove/toggle/check
    useProgress.ts             — per-subject progress recording + overall stats
    useQuiz.ts                 — quiz engine: timer, answer tracking, mark for review, auto-submit
  components/
    layout/Sidebar.tsx         — collapsible desktop sidebar + mobile drawer
    layout/Header.tsx          — page title, global search, streak badge, theme toggle
    layout/Layout.tsx          — full-page layout wrapper
    shared/StatCard.tsx        — stat card with icon + value
    shared/SubjectBadge.tsx    — colored subject tag
    quiz/QuizCard.tsx          — question card with options, mark/bookmark, explanation toggle
    quiz/QuizTimer.tsx         — countdown timer with color urgency states
    quiz/ProgressBar.tsx       — flexible progress bar
  pages/
    HomePage.tsx               — dashboard hero, stat cards, subject progress, recent activity
    DashboardPage.tsx          — recharts analytics: line, bar, pie charts + attempt history table
    SubjectsPage.tsx           — all 8 subjects with exam filter, progress, practice/quick buttons
    QuizPage.tsx               — configurable quiz (count, time, difficulty, exam type) + question navigator
    MockTestPage.tsx           — 30Q / 90min mock exam with submit confirmation dialog
    ResultPage.tsx             — score card, subject breakdown, per-question review, bookmark mistakes
    FormulasPage.tsx           — 48 formulas with search, subject filter, variable table, worked examples
    BookmarksPage.tsx          — saved questions & formulas with expand/inline review
```

## Architecture decisions

- **Frontend-only**: No backend or API server. All state is in localStorage under `ee_*` keys.
- **Streak tracking**: `updateStreak()` called after every quiz submission; persisted in `ee_streak`.
- **Attempt history**: Last 50 attempts stored in `ee_history`; used for dashboard charts and result lookup by ID.
- **Quiz engine**: `useQuiz` hook owns all quiz state (timer, answers, marks). QuizPage and MockTestPage both use it with different config.
- **Per-unit result routing**: After submission, `generateAttemptId()` creates a unique key, attempt is saved, then navigation goes to `/result/:id` which reads back from localStorage.

## Product

VoltPrep covers all 8 core EE subjects (Network Theory, Electrical Machines, Power Systems, Control Systems, Power Electronics, Measurements, Analog Electronics, Digital Electronics) with:
- 80+ MCQ questions with detailed explanations, difficulty ratings, exam tags
- 48 formulas with variable tables and worked examples
- Configurable quizzes: number of questions, time limit, difficulty, exam type
- 90-minute full mock test spanning all subjects
- Subject-wise progress tracking with accuracy charts
- Bookmark system for questions and formulas
- Global search across questions and formulas
- Study streak tracking
- Light/dark mode toggle (dark is default)

## User preferences

- Dark navy + electric blue theme (default dark mode)
- App name: VoltPrep
- No backend — all localStorage
- All 8 EE subjects
- Exams: GATE, SSC-JE, ESE, Diploma, B.Tech

## Gotchas

- Do NOT add routing `base` prefix to internal navigation links — wouter's `Router base` handles it automatically.
- Streak count shows `0d` on fresh install; it updates after first quiz completion.
- `filterQuestions` shuffles before slicing — question order is randomized every time.
- localStorage quota errors are silently swallowed in all storage utils.
