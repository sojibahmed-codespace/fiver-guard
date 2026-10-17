# AGENTS.md — Antigravity Agent Architecture & Coding Standards

> **Agent Role:** Principal Full-Stack TypeScript Engineer & UI/UX Architect  
> **Project:** **FiverrGuard AI** — Standalone Fiverr Inbox Restricted Words Checker & Hybrid Fixer Web App  
> **Tech Stack:** [Next.js 15+ (App Router)](https://www.google.com/search?q=https%3A%2F%2Fnextjs.org%2F), [TypeScript (Strict)](https://www.google.com/search?q=https%3A%2F%2Fwww.typescriptlang.org%2F), [Tailwind CSS](https://www.google.com/search?q=https%3A%2F%2Ftailwindcss.com%2F), [shadcn/ui](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Finstallation%2Fnext), [Tiptap Editor](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Feditor%2Fgetting-started%2Finstall%2Fnextjs), [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fgetting-started%2Fnextjs-app-router), [Zustand](https://www.google.com/search?q=https%3A%2F%2Fzustand.docs.pmnd.rs%2F), [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F), [Lucide React](https://www.google.com/search?q=https%3A%2F%2Flucide.dev%2Fguide%2Freact%2F), [Sonner](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Fcomponents%2Fradix%2Fsonner)

## 1. Mission & Core Engineering Principles

You are building **FiverrGuard AI**, a standalone web application focused strictly on **Fiverr Inbox communication safety**. Your code must meet production SaaS quality standards on the first pass:

1. **Zero Placeholders or `TODO` Stubs:** Never leave `// TODO`, `// implement later`, or mock arrays with 3 items. Implement the full 100+ rule Fiverr inbox dictionary, regex obfuscation detectors, whitelist exceptions, and 12+ inbox templates defined in `RESTRICTED_WORDS_ENGINE_SPEC.md`.
2. **True Hybrid Fixing Architecture:**
   - **Engine A (Deterministic Rule-Based Engine):** Runs 100% client-side in `<15ms` on every keystroke. Requires zero network calls or API keys. Provides instant inline highlighting, hover synonym replacement cards, and one-click **"Quick Fix All"**.
   - **Engine B (Context-Aware AI Rewriter):** Runs via Next.js Server Route (`/api/rewrite`) using the [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fintroduction) (`generateObject` with [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F) schema validation). Rewrites full sentences naturally in 3 tones (**Natural & Polite**, **Professional & Concise**, **Client Boundary / Off-Platform Redirect**) and runs a mandatory **Post-Rewrite Deterministic Verification Pass** so the AI never outputs a restricted word itself.
   - **Graceful API Key Fallback:** Support both server-side `.env.local` (`GOOGLE_GENERATIVE_AI_API_KEY` or `OPENAI_API_KEY`) AND a client-side "Bring Your Own Key (BYOK)" settings modal (stored only in browser `localStorage`), plus an intelligent **Smart Template + Clause Rebuilder fallback** so the app works out-of-the-box even before an API key is entered.
3. **Strict Type Safety:** Enable `"strict": true` in `tsconfig.json`. Never use `any` or `@ts-ignore`. Validate all external inputs and LLM outputs with [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F).

## 2. Canonical Directory Structure

Follow this exact file tree so modules remain decoupled, testable, and easy to maintain:

```text
fiverrguard-ai/
├── AGENTS.md                                  # Master instructions for Antigravity
├── docs/
│   ├── PRD_AND_ROADMAP.md                     # Product requirements & roadmap
│   ├── RESTRICTED_WORDS_ENGINE_SPEC.md        # Dictionary & hybrid algorithm spec
│   └── AGENT_EXECUTION_PLAYBOOK.md            # Step-by-step build & verification playbook
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   └── rewrite/
│   │   │       └── route.ts                   # Vercel AI SDK structured rewrite endpoint
│   │   ├── globals.css                        # Tailwind imports + Tiptap highlight styles
│   │   ├── layout.tsx                         # Root layout, fonts, ThemeProvider, Sonner Toaster
│   │   └── page.tsx                           # Main split-pane workspace view
│   ├── components/
│   │   ├── editor/
│   │   │   ├── InboxEditor.tsx                # Tiptap rich editor + toolbar + char counter
│   │   │   ├── RiskHighlightExtension.ts      # ProseMirror plugin for inline squiggly decorations
│   │   │   ├── WordFixPopover.tsx             # Floating popover when clicking/hovering a flagged word
│   │   │   └── DiffComparisonView.tsx         # Word-by-word inline diff viewer (Original vs Fixed)
│   │   ├── sidebar/
│   │   │   ├── SafetyScoreCard.tsx            # 0-100% radial/linear safety gauge & risk breakdown
│   │   │   ├── FlaggedIssuesList.tsx          # Grouped list of detected violations + 1-click fixes
│   │   │   └── AiRewritePanel.tsx             # Tone selector, 3 AI rewrite cards, & Apply button
│   │   ├── drawers/
│   │   │   ├── SafeTemplatesDrawer.tsx        # 12+ pre-built Fiverr Inbox safe response templates
│   │   │   ├── CustomDictionaryModal.tsx      # Add/edit custom banned words & safe whitelists
│   │   │   └── ApiKeySettingsModal.tsx        # Optional BYOK Gemini/OpenAI API key configuration
│   │   └── ui/                                # shadcn/ui primitives (button, badge, dialog, etc.)
│   ├── lib/
│   │   ├── engine/
│   │   │   ├── types.ts                       # Core interfaces: RiskLevel, RuleEntry, MatchSpan
│   │   │   ├── dictionary.ts                  # Complete Fiverr Inbox restricted words & regex rules
│   │   │   ├── obfuscation.ts                 # Detects l33tspeak, spaced letters, "at/dot" emails
│   │   │   ├── scanner.ts                     # Deterministic scanner, whitelist filter & safety scorer
│   │   │   ├── ruleFixer.ts                   # Instant 1-click token & clause replacement engine
│   │   │   └── templates.ts                   # 12+ Fiverr Inbox safe reply templates
│   │   ├── store/
│   │   │   └── useWorkspaceStore.ts           # Zustand persisted store (text, history, custom rules)
│   │   └── utils.ts                           # cn() helper & clipboard utilities
│   └── __tests__/
│       └── scanner.test.ts                    # Vitest suite for scanner, obfuscation & rule fixer
├── package.json
├── tsconfig.json
└── tailwind.config.ts
```

## 3. Required NPM Packages & Installation Commands

When initializing the repository, install these exact packages:

```bash
# 1. Core UI, Icons, State, Validation, Diffing & Notifications
npm install zustand zod lucide-react sonner diff clsx tailwind-merge class-variance-authority framer-motion

# 2. Tiptap Headless Editor & ProseMirror Decorations
npm install @tiptap/react @tiptap/pm @tiptap/starter-kit @tiptap/extension-placeholder @tiptap/extension-character-count

# 3. Vercel AI SDK (Supports both Google Gemini & OpenAI)
npm install ai @ai-sdk/google @ai-sdk/openai

# 4. Dev & Testing Dependencies
npm install -D @types/diff vitest
```

## 4. UI/UX & Visual Design Guardrails

- **Aesthetic Persona:** High-craft, distraction-free workspace inspired by Linear, Grammarly, and Raycast. Clean slate/zinc neutrals (`#09090B` dark mode, `#FAFAFA` light mode) paired with Fiverr-inspired emerald accents (`#1DBF73`) for safe states.
- **Semantic Risk Color System (Must Meet WCAG AA ≥4.5:1 Contrast):**
  - `CRITICAL` (Direct Ban / Payment / Off-Platform App): Red underline (`#EF4444`), soft badge background (`bg-red-500/10 text-red-600 dark:text-red-400 border-red-500/20`).
  - `HIGH` (Payment / Review / Fee Trigger): Amber underline (`#F59E0B`), soft badge background (`bg-amber-500/10 text-amber-600 dark:text-amber-400 border-amber-500/20`).
  - `CONTEXTUAL` (Warning Banner Trigger — Check Context): Blue dotted underline (`#3B82F6`), soft badge background (`bg-blue-500/10 text-blue-600 dark:text-blue-400 border-blue-500/20`).
  - `SAFE / FIXED`: Emerald accent (`#10B981` / `#1DBF73`), soft badge background (`bg-emerald-500/10 text-emerald-600 dark:text-emerald-400 border-emerald-500/20`).
- **Desktop Split-Pane Layout (≥1024px):**
  - **Left Pane (60% width):** Fiverr Inbox Composer header (Buyer Name context field, Quick Sample Message loader, Template Picker button), Tiptap Rich Editor with live inline underlines, bottom status bar (`0 / 2,500 characters`, word count, reading time, `"Quick Fix All"` button, `"Copy Safe Message"` primary CTA).
  - **Right Pane (40% width):** Sticky Compliance & Fixer Inspector with 2 tabs:
    - **Tab 1: Detected Risks (`N` issues):** Safety Score gauge (`0–100%`), filter pills (`All`, `Critical`, `High`, `Contextual`), and interactive violation cards with one-click synonym chips.
    - **Tab 2: AI Smart Rewriter:** Tone selector (`Natural & Polite`, `Professional & Concise`, `Client Boundary Redirect`), **"Rewrite with AI"** button, 3 generated safe message cards with word-level diff toggle, and **"Apply to Editor"** button.

## 5. Critical Implementation Rules for Tiptap & ProseMirror

1. **SSR Hydration Prevention:** Always pass `immediatelyRender: false` inside `useEditor({ ... })` as required by [Tiptap Next.js Documentation](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Feditor%2Fgetting-started%2Finstall%2Fnextjs) to prevent React 19 / Next.js hydration mismatches.
2. **Non-Destructive Highlighting via Decorations:** Never mutate the document HTML with `<mark>` tags to highlight restricted words (which breaks undo history and cursor position). Instead, use a custom ProseMirror `Plugin` with `Decoration.inline(from, to, { class: 'risk-underline-critical', 'data-match-id': id })` as documented in [Tiptap React Decorations](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Fguides%2Fdecorations-react).
3. **Accurate Position Mapping:** When iterating over `doc.descendants((node, pos) => ...)`, run the scanner on each text node and offset match indices by `pos` (`from = pos + match.start`, `to = pos + match.end`) so replacements via `editor.chain().focus().insertContentAt({ from, to }, replacement).run()` land on the exact character range.

## 6. Mandatory Self-Verification Protocol

After completing each milestone in `PRD_AND_ROADMAP.md`, you **MUST** run the following verification commands in the terminal and fix any issues before moving to the next phase:

```bash
# 1. Verify TypeScript compilation
npx tsc --noEmit

# 2. Run unit tests for the detection & fixing engine
npx vitest run

# 3. Verify production Next.js build
npm run build
```

Do not consider the project complete until all three commands pass with zero errors.
