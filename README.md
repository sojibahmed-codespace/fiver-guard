# FiverrGuard AI 🛡️

**FiverrGuard AI** is a standalone, ultra-responsive web application designed for Fiverr freelancers and agencies to ensure 100% compliance with Fiverr's Terms of Service during buyer-seller inbox communications.

It features a hybrid detection and fixing engine:
- **Engine A (Deterministic Client-Side Engine):** <15ms instant keystroke scanning, inline severity highlighting, and 1-click Quick Fix All without requiring any API keys.
- **Engine B (Context-Aware AI Rewriter):** Multi-tone safe rewriting powered by Vercel AI SDK (with Gemini / OpenAI BYOK options and local smart fallback), with a mandatory post-generation sanitization gate.

---

## 📚 Project Documentation

The documentation has been organized according to the architecture specification:

- [**`AGENTS.md`**](file:///c:/Users/Sabbir%20Rahman%20Softve/Downloads/Client/sojibahmed/fiver-guard/AGENTS.md) — Master instructions, engineering principles, stack requirements, and self-verification protocols.
- [**`docs/PRD_AND_ROADMAP.md`**](file:///c:/Users/Sabbir%20Rahman%20Softve/Downloads/Client/sojibahmed/fiver-guard/docs/PRD_AND_ROADMAP.md) — Product Requirements Document (PRD), feature specs, and 5-phase implementation roadmap.
- [**`docs/RESTRICTED_WORDS_ENGINE_SPEC.md`**](file:///c:/Users/Sabbir%20Rahman%20Softve/Downloads/Client/sojibahmed/fiver-guard/docs/RESTRICTED_WORDS_ENGINE_SPEC.md) — Complete 100+ rule Fiverr inbox dictionary, regex obfuscation engine, and API routes.
- [**`docs/AGENT_EXECUTION_PLAYBOOK.md`**](file:///c:/Users/Sabbir%20Rahman%20Softve/Downloads/Client/sojibahmed/fiver-guard/docs/AGENT_EXECUTION_PLAYBOOK.md) — Autonomous master prompts, phased build prompts, and end-to-end verification test cases.

---

## 🛠️ Tech Stack

- **Framework:** [Next.js 15+ (App Router)](https://nextjs.org/)
- **Language:** [TypeScript](https://www.typescriptlang.org/) (Strict Mode)
- **Styling:** [Tailwind CSS](https://tailwindcss.com/) + [shadcn/ui](https://ui.shadcn.com/)
- **Editor:** [Tiptap Editor](https://tiptap.dev/) with custom ProseMirror inline decorations
- **AI Integration:** [Vercel AI SDK](https://sdk.vercel.ai/) (`ai`, `@ai-sdk/google`, `@ai-sdk/openai`)
- **State Management:** [Zustand](https://zustand.docs.pmnd.rs/) with `persist` middleware
- **Validation:** [Zod](https://zod.dev/)
- **Testing:** [Vitest](https://vitest.dev/)

---

## 🚀 Getting Started

1. Install dependencies:
```bash
npm install
```

2. Run the development server:
```bash
npm run dev
```

3. Open [http://localhost:3000](http://localhost:3000) with your browser to start using the app.
