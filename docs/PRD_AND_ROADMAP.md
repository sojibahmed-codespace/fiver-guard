# FiverrGuard AI — Product Requirements Document (PRD) & Implementation Roadmap

**Product Name:** FiverrGuard AI (Fiverr Inbox Compliance & Smart Message Fixer)  
**Platform:** Standalone Web Application ([Next.js App Router](https://www.google.com/search?q=https%3A%2F%2Fnextjs.org%2F) + [TypeScript](https://www.google.com/search?q=https%3A%2F%2Fwww.typescriptlang.org%2F) + [Tailwind CSS](https://www.google.com/search?q=https%3A%2F%2Ftailwindcss.com%2F))  
**Core Engine:** Hybrid Detection & Fixing Engine (Deterministic Rule/Regex Dictionary + Context-Aware LLM Rewriter via [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fintroduction))  
**Target Scope:** Strictly Fiverr Inbox Communication (Buyer-Seller Chat & Custom Offer Messages)

## 1. Executive Summary & Product Vision

Freelancers on Fiverr frequently receive automated account warnings, restricted inbox privileges, or permanent account suspensions ([Medium Case Study](https://www.google.com/search?q=https%3A%2F%2Fmedium.com%2F%40christopherevans3136%2Fmy-fiverr-seller-account-was-permanently-banned-within-hours-heres-what-happened-and-why-you-e44d93509713)) simply for typing trigger words in the Fiverr inbox—even when their intent is completely innocent (for example, saying *"I will email you the rough layout inside the order page"*, *"Pay attention to the header design"*, or *"Let's do a quick Zoom call"* without using Fiverr's official Zoom integration) ([Reddit r/Fiverr Discussion](https://www.google.com/search?q=https%3A%2F%2Fwww.reddit.com%2Fr%2FFiverr%2Fcomments%2Fwo2cik%2Fhelp_can_i_get_a_strike_if_i_ignore_the_warning%2F)).

**FiverrGuard AI** is a standalone, ultra-responsive web application built for Fiverr sellers and agencies. Users paste or draft their Fiverr inbox replies in a rich editor that performs **zero-latency client-side scanning** alongside **context-aware AI rewriting**:

1. **Instant Rule-Based Detection (<15ms):** Highlights restricted words, obfuscated evasions (such as `wh@tsapp` or `p-a-y-p-a-l`), email addresses, phone numbers, and unapproved external URLs color-coded by risk severity, while filtering out safe contextual idioms (like *"pay attention"* or *"email marketing template"*).
2. **Hybrid 2-Tier Fixing System:**
   - **Tier 1 (Instant Rule-Based Fixer):** Click any flagged word or hit **"Quick Fix All"** to swap restricted terms with Fiverr-safe synonyms and compliant terminology in 0ms without an API call.
   - **Tier 2 (Context-Aware AI Rewriter):** Uses the [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fnextjs-app-router) to rewrite the entire message (or specific flagged sentences) so the natural flow, persuasion, and client rapport remain intact while achieving a **100% Fiverr Inbox Safety Score**.

## 2. Target User Workflows & Core Problems Solved

| Problem Scenario in Fiverr Inbox | Why Basic Word Checkers Fail | How FiverrGuard AI Solves It |
| :--- | :--- | :--- |
| **Innocent Trigger Words:** Typing *"pay"*, *"money"*, *"email"*, or *"review"* triggers Fiverr's automated inbox warning popup ([Reddit r/Fiverr](https://www.google.com/search?q=https%3A%2F%2Fwww.reddit.com%2Fr%2FFiverr%2Fcomments%2Fwo2cik%2Fhelp_can_i_get_a_strike_if_i_ignore_the_warning%2F)). | Basic word checkers replace words blindly (`"pay attention"` becomes `"compensate attention"`), ruining grammar. | Context-aware whitelist rules recognize safe idioms, and the hybrid fixer replaces only genuine risk triggers or rewrites the full clause naturally. |
| **Client Pushes for Off-Platform Chat:** A buyer asks to talk on WhatsApp, Telegram, Discord, or personal Zoom ([Fiverr Community Standards Overview](https://www.google.com/search?q=https%3A%2F%2Fwww.facebook.com%2Fgroups%2Fgigdoctor%2Fposts%2F1923447754657929%2F)). | Simply deleting the word `"WhatsApp"` leaves an incomplete reply and doesn't help the seller decline politely without losing the sale. | Includes **Smart Client Deflection Templates** + AI Rewriter mode that politely redirects the buyer to Fiverr Inbox / Fiverr Zoom while keeping the deal alive. |
| **Asking for Feedback Safely:** Mentioning `"5-star review"` or `"positive rating"` can trigger a review-manipulation strike ([BlackHatWorld Forum Analysis](https://www.google.com/search?q=https%3A%2F%2Fwww.blackhatworld.com%2Fseo%2Fbanned-from-fiverr-for-using-the-word-review-on-a-gig-title.1054453%2F)). | Sellers either avoid asking for feedback altogether or use awkward spacing (`r e v i e w`) that Fiverr's NLP still flags. | Rewrites feedback requests into compliant project check-ins (e.g., *"I hope you're thrilled with the delivery and would love to hear your thoughts on the experience"*). |
| **Character Limit & Formatting:** Fiverr inbox messages have a strict 2,500-character ceiling ([Lunexiait Fiverr Checker](https://www.google.com/search?q=https%3A%2F%2Fwww.lunexiait.com%2Fmessage-checker-tools%2F)). | External editors don't track Fiverr's 2,500-character limit or attachment/link policies. | Live character counter (`0 / 2,500`), attachment reminder badges, and one-click **"Copy Safe Message"** with toast confirmation. |

## 3. Recommended "Next-Level" Tech Stack & NPM Libraries

To deliver a fast, polished UX, the coding agent must build the application with the following stack:

| Layer / Capability | Package / Technology | Official Documentation | Purpose in FiverrGuard AI |
| :--- | :--- | :--- | :--- |
| **Framework** | `next` (v15+ App Router) & `react` (v19) | [Next.js Docs](https://www.google.com/search?q=https%3A%2F%2Fnextjs.org%2F) | Server Actions, Route Handlers for AI rewriting, fast client hydration, and edge deployment. |
| **Language** | `typescript` (Strict Mode) | [TypeScript Docs](https://www.google.com/search?q=https%3A%2F%2Fwww.typescriptlang.org%2F) | End-to-end type safety across dictionary schemas, AST token spans, and AI responses. |
| **Styling & UI Primitives** | `tailwindcss` + `shadcn/ui` (`@radix-ui/*`) | [shadcn/ui Next.js](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Finstallation%2Fnext) | Accessible popovers, tooltips, tabs, drawers, command palette (`cmdk`), and dark/light mode. |
| **Rich Highlight Editor** | `@tiptap/react`, `@tiptap/starter-kit`, `@tiptap/pm` | [Tiptap React Docs](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Feditor%2Fgetting-started%2Finstall%2Fnextjs) | Custom ProseMirror decoration plugin ([Tiptap Decorations](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Fguides%2Fdecorations-react)) for inline squiggly underlines and hover fix cards. |
| **AI SDK & Structured Output** | `ai`, `@ai-sdk/google`, `@ai-sdk/openai` | [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fgetting-started%2Fnextjs-app-router) | Streaming sentence rewrites, structured JSON diff explanations, and multi-tone transformations. |
| **Schema Validation** | `zod` | [Zod Docs](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F) | Validates dictionary rules, API payloads, and structured LLM output schemas. |
| **Client State & Persistence** | `zustand` (with `persist` middleware) | [Zustand Docs](https://www.google.com/search?q=https%3A%2F%2Fzustand.docs.pmnd.rs%2F) | Persists custom user dictionary rules, message history, tone preferences, and BYOK API keys in `localStorage`. |
| **Icons & Feedback UI** | `lucide-react` + `sonner` | [Lucide React](https://www.google.com/search?q=https%3A%2F%2Flucide.dev%2Fguide%2Freact%2F) / [Sonner](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Fcomponents%2Fradix%2Fsonner) | Crisp iconography and instant toast notifications when copying sanitized messages or applying fixes. |
| **Text Diffing** | `diff` (`@types/diff`) | [diff Package](https://www.google.com/search?q=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fzod) | Computes word-level inline diffs between the original message and the AI-rewritten message. |

## 4. Core Functional Requirements (Strictly Fiverr Inbox Focused)

### 4.1 Real-Time Inbox Message Editor & Risk Scanner
- **Inline Severity Highlighting:** As the user types or pastes a Fiverr inbox message, the Tiptap decoration layer highlights matches in real time (<15ms debounce):
  - **Critical Risk (Red `#EF4444`):** Direct payment processors (`PayPal`, `Stripe`, `Wise`, `Payoneer`, `Venmo`, `CashApp`, `Crypto`, `USDT`, `bank transfer`), off-platform apps (`WhatsApp`, `Telegram`, `Discord`, `Skype`, `Signal`), and raw email/phone patterns.
  - **High Risk (Amber `#F59E0B`):** Payment & fee triggers (`pay`, `payment`, `money`, `cash`, `fee`, `commission`, `outside Fiverr`, `direct deal`) and review triggers (`5 stars`, `five star`, `positive review`, `rating`, `feedback`, `change review`).
  - **Contextual / Watchlist (Blue `#3B82F6`):** Words that trigger Fiverr's automated warning banner but are permitted in specific contexts (`email`, `call`, `Zoom`, `contact`, `phone`, `link`, `website`, `refund`, `cancel`).
- **Obfuscation & Evasion Detector:** Detects deliberate spacing (`w h a t s a p p`, `p.a.y.p.a.l`), leetspeak (`wh@tsapp`, `p4ypal`, `em@il`), and spelled-out numbers/emails (`john AT gmail DOT com`) and warns the user that Fiverr's moderation AI penalizes deliberate evasion even more harshly than accidental keywords.
- **Context-Aware Whitelist Filter:** Automatically suppresses false alarms when a trigger word is part of a safe, whitelisted Fiverr service phrase (e.g., `"email marketing campaign"`, `"landing page conversion"`, `"pay attention to"`, `"call to action"`).

### 4.2 Hybrid Fixing Engine (Rule-Based + AI)
- **Interactive Hover / Click Popover on Flagged Words:**
  - Displays the **Risk Category**, **Why Fiverr Flags This**, and **3 Instant Synonym/Phrase Replacements** (Rule-Based Tier 1).
  - Clicking a replacement swaps the exact token span in the editor immediately and recalculates the Safety Score.
- **"Quick Fix All (Rule-Based)" Button:**
  - Deterministically replaces all Critical and High-Risk tokens using the primary context-safe replacement from the local dictionary in under 10ms—working 100% offline with zero API calls.
- **"Smart AI Rewrite (Context-Aware)" Panel:**
  - Powered by [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fintroduction), sends the original message plus the array of detected violations to `/api/rewrite`.
  - Generates **3 distinct Fiverr-safe variations** tailored to the selected tone:
    1. **Natural & Polite (Default):** Closest to the user's original wording with all triggers smoothly rephrased.
    2. **Professional & Concise:** Crisp, business-grade response suitable for Fiverr Pro / high-ticket buyers.
    3. **Client Boundary / Redirect:** Specifically designed for when a buyer asks to move off Fiverr or pay externally—politely declines off-platform contact while reassuring the buyer that everything can be handled seamlessly inside the Fiverr order page or via Fiverr's built-in Zoom call feature.
  - Includes a **Visual Word Diff Toggle** (highlighting removed risky phrases in strikethrough red and safe replacements in emerald green) and a **Post-Rewrite Verification Gate** (automatically runs the deterministic scanner on the AI output to guarantee 0 restricted words remain).

### 4.3 Fiverr Inbox Safety Score & Analytics Sidebar
- **Live Safety Gauge (0–100%):**
  - `100% (Safe to Send - Emerald)`: Zero Critical or High-Risk triggers; optional contextual notes only.
  - `60%–99% (Caution - Amber)`: Contains contextual watchlist words that may trigger Fiverr's "Remind you of our Terms of Service" pop-up banner.
  - `0%–59% (Account Strike Risk - Red)`: Contains Critical or High-Risk terms that risk message blocking, human moderation review, or account warning.
- **Fiverr Inbox Constraints Bar:**
  - Character count (`0 / 2,500 chars`) with warning at `2,300+`.
  - URL Scanner badge (verifies whether any pasted URL is on Fiverr's allowed portfolio domains list vs. unapproved external links).

### 4.4 Inbox Scenario Templates & Custom Dictionary Manager
- **Pre-Built Fiverr Inbox Safe Replies Library:**
  - 12+ battle-tested, 100% Fiverr-safe templates for tricky inbox moments:
    - *Declining a buyer's WhatsApp/Telegram request without losing the order*
    - *Explaining that all payments must go through a Fiverr Custom Offer*
    - *Requesting temporary login credentials safely for a Gig requirement*
    - *Asking for honest order completion & feedback without triggering review manipulation flags*
    - *Proposing a video call using Fiverr's official Zoom integration*
- **Custom Dictionary & Whitelist Drawer:**
  - Users can add their own custom banned words, preferred safe synonyms, or niche-specific whitelisted phrases (stored locally via [Zustand](https://www.google.com/search?q=https%3A%2F%2Fzustand.docs.pmnd.rs%2F) `persist` with JSON export/import).

## 5. Phased Implementation Roadmap

| Phase | Milestone Name | Key Deliverables | Verification Gate |
| :--- | :--- | :--- | :--- |
| **Phase 1** | **Project Scaffolding & Design System** | Initialize [Next.js App Router](https://www.google.com/search?q=https%3A%2F%2Fnextjs.org%2F) + [TypeScript](https://www.google.com/search?q=https%3A%2F%2Fwww.typescriptlang.org%2F) + [Tailwind CSS](https://www.google.com/search?q=https%3A%2F%2Ftailwindcss.com%2F) + [shadcn/ui](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Finstallation%2Fnext). Set up dark/light theme, split-pane layout shell, header navigation, and [Zustand](https://www.google.com/search?q=https%3A%2F%2Fzustand.docs.pmnd.rs%2F) store. | `npm run typecheck && npm run build` passes with zero errors; responsive layout renders at 375px, 768px, and 1440px. |
| **Phase 2** | **Deterministic Rule Engine & Dictionary** | Build `src/lib/engine/dictionary.ts` (100+ categorized Fiverr inbox triggers, regex detectors for emails/phones/obfuscations, context whitelists) and `src/lib/engine/scanner.ts` (token offset scanner + Safety Score calculator + deterministic Quick Fixer). | Unit test suite (`vitest`) passes 100% of test cases: standard triggers, obfuscated words (`wh@tsapp`), safe idioms (`pay attention`), and 1-click replacements. |
| **Phase 3** | **Interactive Tiptap Editor & Popover Fixer** | Build the [Tiptap](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Feditor%2Fgetting-started%2Finstall%2Fnextjs) editor with custom ProseMirror decoration plugin (`src/components/editor/HighlightExtension.ts`), inline severity underlines, click-to-fix popover cards, character limit bar (`2,500`), and 1-click Copy Safe Message with [Sonner](https://www.google.com/search?q=https%3A%2F%2Fui.shadcn.com%2Fdocs%2Fcomponents%2Fradix%2Fsonner) toasts. | Typing or pasting a 2,000-character message highlights all restricted spans in <15ms and clicking a synonym replaces the exact span without cursor jump. |
| **Phase 4** | **Context-Aware AI Rewriter & Diff View** | Build `/api/rewrite/route.ts` using [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fgetting-started%2Fnextjs-app-router) and [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F) structured output (`RewriteResponseSchema`). Implement multi-tone selector, word-level diff toggle, BYOK API key fallback modal, and automatic post-generation rule scan. | AI rewrites any flagged inbox message into 3 compliant options, passes the deterministic post-check with 0 remaining flags, and gracefully falls back to enhanced rule-based rewriting if no API key is configured. |
| **Phase 5** | **Safe Reply Templates, Custom Rules & Polish** | Build the Safe Inbox Templates drawer (12+ scenarios), Custom Dictionary manager (add/edit/import/export rules), keyboard shortcuts (`Cmd/Ctrl + Enter` to fix, `Cmd/Ctrl + Shift + C` to copy), and Recent Fixes history drawer. | Full end-to-end build (`npm run lint && npm run typecheck && npm run build`) succeeds with zero warnings. |
