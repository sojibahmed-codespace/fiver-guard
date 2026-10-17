# FiverrGuard AI — Antigravity Agent Execution Playbook

Use this playbook to instruct **Antigravity** to build, test, and verify the entire **FiverrGuard AI** standalone web application from start to finish.

## How to Use These Docs in Antigravity

1. Create a new project folder (e.g., `fiverrguard-ai`) and open it in **Antigravity**.
2. Place `AGENTS.md` in the root of your workspace (`/AGENTS.md`) and place `PRD_AND_ROADMAP.md`, `RESTRICTED_WORDS_ENGINE_SPEC.md`, and `AGENT_EXECUTION_PLAYBOOK.md` inside a `/docs` folder (or the root folder).
3. Copy and paste the **Master One-Shot Build Prompt** below to let Antigravity build the entire app autonomously, OR run the **5 Phased Prompts** one by one if you want to inspect each milestone interactively.

## Option A: Master One-Shot Autonomous Build Prompt (Copy & Paste into Antigravity)

> Read `AGENTS.md`, `PRD_AND_ROADMAP.md`, and `RESTRICTED_WORDS_ENGINE_SPEC.md` in full before writing any code.
>
> Build the complete **FiverrGuard AI** standalone web application (strictly focused on Fiverr Inbox message checking and hybrid fixing) using [Next.js App Router](https://www.google.com/search?q=https%3A%2F%2Fnextjs.org%2F), [TypeScript](https://www.google.com/search?q=https%3A%2F%2Fwww.typescriptlang.org%2F), [Tailwind CSS](https://www.google.com/search?q=https%3A%2F%2Ftailwindcss.com%2F), [shadcn/ui](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Finstallation%2Fnext), [Tiptap Editor](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Feditor%2Fgetting-started%2Finstall%2Fnextjs), [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fgetting-started%2Fnextjs-app-router), [Zustand](https://www.google.com/search?q=https%3A%2F%2Fzustand.docs.pmnd.rs%2F), [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F), [Lucide React](https://www.google.com/search?q=https%3A%2F%2Flucide.dev%2Fguide%2Freact%2F), and [Sonner](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Fcomponents%2Fradix%2Fsonner).
>
> Execute all 5 phases sequentially without stopping for confirmation:
> 1. **Phase 1 (Scaffolding & UI Shell):** Initialize the Next.js TypeScript project, install all required packages listed in `AGENTS.md`, configure dark/light mode, and create the responsive split-pane workspace shell.
> 2. **Phase 2 (Deterministic Detection Engine & Unit Tests):** Implement `src/lib/engine/types.ts`, `src/lib/engine/dictionary.ts` (all 5 categories + safe whitelists from `RESTRICTED_WORDS_ENGINE_SPEC.md`), `src/lib/engine/obfuscation.ts` (email, phone, spaced/dotted words, and leetspeak regexes), `src/lib/engine/scanner.ts`, `src/lib/engine/ruleFixer.ts`, and `src/lib/engine/templates.ts` (12+ Fiverr Inbox safe reply templates). Write and run the Vitest test suite (`src/__tests__/scanner.test.ts`).
> 3. **Phase 3 (Interactive Tiptap Editor & Instant Rule Fixer):** Build `InboxEditor.tsx` with `immediatelyRender: false` and a custom ProseMirror Decoration plugin (`RiskHighlightExtension.ts`) that underlines `CRITICAL` (red), `HIGH` (amber), and `CONTEXTUAL` (blue) spans in `<15ms`. Add the interactive click/hover `WordFixPopover.tsx` for 1-click synonym replacement, the `"Quick Fix All"` button, the `2,500` character counter, and 1-click `"Copy Safe Message"` with Sonner toast feedback.
> 4. **Phase 4 (Hybrid AI Rewriter & Word-Level Diff Viewer):** Implement `/api/rewrite/route.ts` using Vercel AI SDK `generateObject` + Zod schema validation with automatic fallback to smart local clause rewriting when no API key is configured, plus a mandatory post-generation deterministic verification pass. Build `AiRewritePanel.tsx`, `DiffComparisonView.tsx` (using `diff`), and `ApiKeySettingsModal.tsx` (supporting Gemini and OpenAI keys in `localStorage`).
> 5. **Phase 5 (Templates Drawer, Custom Dictionary & Final Verification):** Build `SafeTemplatesDrawer.tsx`, `CustomDictionaryModal.tsx` (persisted via Zustand), and interactive sample messages ("Load Risky Sample Message") so I can test the checker and fixer immediately. Run `npx tsc --noEmit`, `npx vitest run`, and `npm run build` and fix any issues until all three pass with zero errors.

## Option B: Step-by-Step Phased Prompts (For Granular Control)

### Step 1 Prompt — Core Engine, Dictionary & Unit Tests
> Read `AGENTS.md` and `RESTRICTED_WORDS_ENGINE_SPEC.md`. First, initialize the Next.js + TypeScript + Tailwind CSS project if not already initialized, and install all dependencies from `AGENTS.md`. Next, build the complete `src/lib/engine/` module (`types.ts`, `dictionary.ts`, `obfuscation.ts`, `scanner.ts`, `ruleFixer.ts`, and `templates.ts`). Make sure `scanner.ts` respects `whitelistContexts` (so phrases like `"pay attention"` or `"email marketing"` are not falsely flagged) while catching standard triggers, emails, phone numbers, spaced letters (`w h a t s a p p`), and leetspeak (`p@ypal`). Write a comprehensive test suite in `src/__tests__/scanner.test.ts` and run `npx vitest run` until all tests pass.

### Step 2 Prompt — Tiptap Editor with ProseMirror Inline Highlighting & Popover Fixer
> Following `AGENTS.md`, build the left-pane editor experience in `src/components/editor/`:
> - `RiskHighlightExtension.ts`: A ProseMirror plugin that converts `DetectedMatch[]` into inline decorations (`risk-critical`, `risk-high`, `risk-contextual`) without mutating document HTML or breaking undo history.
> - `InboxEditor.tsx`: Configured with `immediatelyRender: false`, a top bar featuring a **"Load Risky Inbox Sample"** dropdown (with 4 realistic risky buyer/seller inbox drafts), a live `0 / 2,500` character progress bar, a **"Quick Fix All (Instant)"** button, and a **"Copy Safe Message"** button.
> - `WordFixPopover.tsx`: When clicking any highlighted word in the editor, open a floating popover card displaying the Risk Severity badge, Category, why Fiverr flags the term, and 3 clickable safe replacement buttons that swap the exact text range in place.

### Step 3 Prompt — Right-Pane Safety Inspector & Hybrid AI Rewriter
> Build the right-pane inspector (`src/components/sidebar/`) and the AI route (`src/app/api/rewrite/route.ts`):
> - `SafetyScoreCard.tsx`: Animated 0–100% safety gauge with status badge (`Safe to Send`, `Caution: Warning Banner Risk`, `Critical: Account Strike Risk`) and severity counters.
> - `FlaggedIssuesList.tsx`: Filterable list of all flagged phrases in the current message with 1-click replacement chips and an `"Ignore in this message"` button.
> - `/api/rewrite/route.ts` & `AiRewritePanel.tsx`: Implement the hybrid rewrite API supporting both cloud AI (`@ai-sdk/google` Gemini 2.5 Flash / `@ai-sdk/openai` GPT-4o-mini) and a zero-config local smart clause rebuilder fallback. Display 3 tone variations (`Natural & Polite`, `Professional & Concise`, `Client Boundary Redirect`) with a word-level inline diff toggle (`DiffComparisonView.tsx`) and a 1-click **"Use This Reply"** button.

### Step 4 Prompt — Safe Inbox Templates, Custom Rules & Production Verification
> Build the remaining power-user drawers in `src/components/drawers/`:
> - `SafeTemplatesDrawer.tsx`: Searchable drawer with 12+ ready-to-use Fiverr Inbox responses (declining WhatsApp/Telegram politely, redirecting PayPal requests to Fiverr Custom Offers, scheduling via Fiverr Zoom, requesting feedback safely, handling scope creep).
> - `CustomDictionaryModal.tsx`: Allows adding custom restricted words or custom whitelisted safe phrases, persisted via Zustand in `localStorage` with JSON import/export.
> - `ApiKeySettingsModal.tsx`: Clean modal to optionally save a Gemini or OpenAI API key in `localStorage` and toggle between Instant Hybrid Mode and Cloud AI Mode.
> Finally, run `npx tsc --noEmit`, `npx vitest run`, and `npm run build` and verify there are zero TypeScript or build errors.

## Built-In Test Cases for Agent Verification

Ensure the agent validates the application against these 4 real-world Fiverr Inbox test cases:

| Test Case ID | Input Fiverr Inbox Draft | Expected Detections | Expected Fixed Output (100% Safety Score) |
| :--- | :--- | :--- | :--- |
| **TC-01: Off-Platform & Payment** | *"Hey! Can we hop on WhatsApp or Skype to discuss this? Also you can pay me via PayPal to avoid the 20% Fiverr fee."* | `WhatsApp` (Critical), `Skype` (Critical), `pay` (High), `PayPal` (Critical), `20% Fiverr fee` (High) | *"Hi there! Let's discuss all the details right here in the Fiverr Inbox or via Fiverr's built-in Zoom feature, and we can handle the project smoothly through a Fiverr Custom Offer."* |
| **TC-02: Obfuscation Evasion** | *"Text me on w h a t s a p p or send details to john [at] gmail [dot] com and p@ypal."* | `w h a t s a p p` (Critical Evasion), `john [at] gmail [dot] com` (Critical Evasion), `p@ypal` (Critical Evasion) | *"Feel free to message me right here in this chat thread or share the project files inside our active Order Page and settle via a Fiverr Custom Offer."* |
| **TC-03: Review Manipulation** | *"Here is your final delivery! Please leave me a 5-star positive review and good rating."* | `5-star` (High), `positive review` (High), `rating` (High) | *"Here is your final delivery! Please check over the files to make sure everything meets your goals, and I'd love to hear your honest thoughts on the project experience."* |
| **TC-04: Safe Idiom Whitelist** | *"I will pay attention to your email marketing template and call to action button."* | **0 Detections** (`pay attention`, `email marketing`, and `call to action` are whitelisted) | **100% Safe to Send** (No unnecessary replacements made). |
