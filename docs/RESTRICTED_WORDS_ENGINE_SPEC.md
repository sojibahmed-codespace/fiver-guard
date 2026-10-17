# FiverrGuard AI — Restricted Words & Hybrid Fixing Engine Specification

**Target Module:** `src/lib/engine/*` & `src/app/api/rewrite/route.ts`  
**Stack Dependencies:** [TypeScript](https://www.google.com/search?q=https%3A%2F%2Fwww.typescriptlang.org%2F), [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F), [Vercel AI SDK (ai)](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fgetting-started%2Fnextjs-app-router), [Tiptap Decorations](https://www.google.com/search?q=https%3A%2F%2Ftiptap.dev%2Fdocs%2Fguides%2Fdecorations-react)

## 1. Core TypeScript Interfaces (`src/lib/engine/types.ts`)

```typescript
export type RiskSeverity = 'CRITICAL' | 'HIGH' | 'CONTEXTUAL';

export type RiskCategory =
  | 'OFF_PLATFORM_CONTACT'
  | 'EXTERNAL_PAYMENT'
  | 'REVIEW_MANIPULATION'
  | 'CONTACT_CREDENTIALS'
  | 'EXTERNAL_LINK_POLICY'
  | 'EVASION_OBFUSCATION';

export interface RestrictedRule {
  id: string;
  pattern: string;              // Exact word/phrase or Regex source string
  isRegex?: boolean;
  severity: RiskSeverity;
  category: RiskCategory;
  reason: string;               // Shown in the hover card explaining why Fiverr flags this
  replacements: [string, string, string]; // 3 Fiverr-safe synonyms/phrases (index 0 used by Quick Fix All)
  whitelistContexts?: string[]; // Lowercase phrases where this word is 100% safe (e.g., "pay attention")
}

export interface DetectedMatch {
  id: string;
  ruleId: string;
  matchedText: string;
  start: number;                // 0-indexed start offset in plain text / node
  end: number;                  // 0-indexed end offset in plain text / node
  severity: RiskSeverity;
  category: RiskCategory;
  reason: string;
  replacements: string[];
}

export interface ScanResult {
  matches: DetectedMatch[];
  safetyScore: number;          // 0 to 100
  statusLabel: 'Safe to Send' | 'Caution: Warning Banner Risk' | 'Critical: Account Strike Risk';
  counts: {
    critical: number;
    high: number;
    contextual: number;
  };
  charCount: number;
  exceedsFiverrLimit: boolean;  // true if charCount > 2500
}
```

## 2. Comprehensive Fiverr Inbox Restricted Words Matrix (`src/lib/engine/dictionary.ts`)

Fiverr's automated inbox filter scans every message for terms associated with off-platform communication ([Fiverr Community Standards](https://www.google.com/search?q=https%3A%2F%2Fwww.facebook.com%2Fgroups%2Fgigdoctor%2Fposts%2F1923447754657929%2F)), outside payments ([Reddit r/Fiverr Warning Analysis](https://www.google.com/search?q=https%3A%2F%2Fwww.reddit.com%2Fr%2FFiverr%2Fcomments%2Fwo2cik%2Fhelp_can_i_get_a_strike_if_i_ignore_the_warning%2F)), and feedback manipulation ([BlackHatWorld Case Study](https://www.google.com/search?q=https%3A%2F%2Fwww.blackhatworld.com%2Fseo%2Fbanned-from-fiverr-for-using-the-word-review-on-a-gig-title.1054453%2F)). The agent must populate `src/lib/engine/dictionary.ts` with all of the following rules:

### 2.1 Category A: Off-Platform Communication Apps (`CRITICAL` — `#EF4444`)

| Trigger Word / Phrase | Why Fiverr Flags It | 3 Safe Replacements (`replacements[0..2]`) | Whitelisted Safe Contexts |
| :--- | :--- | :--- | :--- |
| `whatsapp` / `whats app` / `wa.me` | Directing buyers to WhatsApp violates Fiverr's off-platform communication policy. | `Fiverr Inbox`, `this chat thread`, `our order messages` | `whatsapp business api integration`, `whatsapp icon` |
| `telegram` / `t.me` | Telegram is heavily flagged for off-platform solicitation. | `Fiverr Inbox`, `this message thread`, `the order page` | `telegram bot development`, `telegram api` |
| `discord` | External voice/chat servers are prohibited before and during orders. | `Fiverr Inbox`, `Fiverr Zoom call`, `this chat` | `discord bot`, `discord server setup gig` |
| `skype` | Legacy off-platform video/voice tool banned in Fiverr chat. | `Fiverr Zoom call`, `Fiverr Inbox`, `order page chat` | *(none)* |
| `signal app` / `wechat` / `viber` / `imo` | External messaging apps trigger instant moderation alerts. | `Fiverr Inbox`, `our chat here`, `the order page` | *(none)* |
| `google meet` / `gmeet` / `microsoft teams` / `facetime` | Unrecorded external video calls bypass Fiverr's built-in Zoom tool. | `Fiverr's built-in Zoom call`, `a Fiverr video call`, `Fiverr Inbox` | *(none)* |
| `dm me on` / `text me on` / `reach me outside` | Explicit off-platform contact solicitation. | `message me right here`, `reply in this thread`, `update me on the order page` | *(none)* |
| `instagram` / `facebook` / `linkedin` / `twitter` | Sharing personal social handles to chat outside Fiverr is restricted. | `my Fiverr portfolio`, `this Fiverr chat`, `the order requirements section` | `instagram reel design`, `facebook ads campaign`, `linkedin banner`, `social media post` |

### 2.2 Category B: External Payment Processors & Financial Triggers (`CRITICAL` — `#EF4444`)

| Trigger Word / Phrase | Why Fiverr Flags It | 3 Safe Replacements (`replacements[0..2]`) | Whitelisted Safe Contexts |
| :--- | :--- | :--- | :--- |
| `paypal` / `pay pal` / `paypal.me` | Soliciting or mentioning PayPal in chat triggers an automatic payment-circumvention flag. | `a Fiverr Custom Offer`, `Fiverr checkout`, `the order page` | `paypal payment gateway integration`, `paypal webhook` |
| `stripe` | Flagged as an external payment processor unless clearly part of web dev scope. | `Fiverr checkout`, `a Custom Offer`, `platform billing` | `stripe api integration`, `stripe checkout webhook`, `stripe sdk` |
| `payoneer` / `wise` / `transferwise` | Direct international transfer services are strictly forbidden between buyers and sellers. | `a Fiverr Custom Offer`, `Fiverr milestone order`, `Fiverr payment system` | *(none)* |
| `venmo` / `cashapp` / `cash app` / `zelle` | Peer-to-peer payment apps trigger immediate account review. | `a Fiverr Custom Offer`, `this Fiverr order`, `a Gig Extra` | *(none)* |
| `crypto` / `bitcoin` / `btc` / `usdt` / `ethereum` / `binance` | Cryptocurrency payment mentions are high-priority ban triggers. | `a Fiverr Custom Offer`, `standard Fiverr order`, `milestone delivery` | `crypto token website`, `blockchain ui design` |
| `bank transfer` / `wire transfer` / `iban` / `swift code` / `routing number` | Sharing banking details in Fiverr chat causes immediate suspension. | `a Fiverr Custom Offer`, `Fiverr billing`, `the order page` | *(none)* |
| `western union` / `moneygram` / `skrill` / `revolut` | External remittance services violate Fiverr Payment Terms. | `a Fiverr Custom Offer`, `Fiverr checkout`, `a Gig Extra` | *(none)* |

### 2.3 Category C: General Payment, Fee & Off-Platform Deal Words (`HIGH` — `#F59E0B`)

| Trigger Word / Phrase | Why Fiverr Flags It | 3 Safe Replacements (`replacements[0..2]`) | Whitelisted Safe Contexts |
| :--- | :--- | :--- | :--- |
| `pay` / `paying` | Triggers Fiverr's automated payment warning popup even in innocent sentences. | `proceed with the order`, `cover via a Custom Offer`, `confirm the package` | `pay attention`, `apple pay integration`, `google pay button` |
| `payment` / `payments` | High-sensitivity financial trigger word in inbox chat. | `order confirmation`, `Custom Offer`, `milestone step` | `payment gateway`, `payment integration` |
| `money` / `cash` / `bucks` / `dollars` | Financial nouns frequently flag automated anti-circumvention filters. | `budget`, `project scope`, `package rate` | *(none)* |
| `fiverr fee` / `20% fee` / `platform fee` / `commission` | Discussing or complaining about Fiverr's 20% service fee is monitored for fee avoidance. | `project total`, `standard package scope`, `Custom Offer total` | *(none)* |
| `outside fiverr` / `off fiverr` / `off-platform` / `work directly` | Phrase matching for platform circumvention. | `right here on Fiverr`, `through our Fiverr order page`, `via a Custom Offer` | *(none)* |
| `cheaper outside` / `discount if you pay` | Direct violation of Fiverr's anti-circumvention policy. | `a tailored Custom Offer`, `a milestone package`, `an adjusted scope` | *(none)* |

### 2.4 Category D: Review & Rating Manipulation (`HIGH` — `#F59E0B`)

| Trigger Word / Phrase | Why Fiverr Flags It | 3 Safe Replacements (`replacements[0..2]`) | Whitelisted Safe Contexts |
| :--- | :--- | :--- | :--- |
| `5 star` / `5-star` / `five star` / `five-star` | Asking for a specific star rating violates Fiverr's Community Standards on review manipulation. | `your honest thoughts`, `your experience with the project`, `confirmation that everything looks great` | *(none)* |
| `positive review` / `good review` / `best review` | Pressuring or soliciting a positive review can lead to a formal account warning. | `your thoughts on the delivery`, `confirming the delivery meets your goals`, `sharing your experience` | *(none)* |
| `review` / `reviews` | Alone, `"review"` often triggers the automated inbox warning banner unless used to mean "inspect the file". | `check over`, `inspect`, `look through` | `code review`, `literature review` |
| `rating` / `ratings` | Flagged as feedback solicitation. | `satisfaction`, `project outcome`, `delivery check` | *(none)* |
| `feedback` | Often paired with review requests; safer to use project-check wording. | `thoughts on the draft`, `notes on the files`, `revision notes` | *(none)* |
| `change your review` / `edit the review` / `remove the review` | Strictly prohibited; coercing a buyer to modify feedback causes immediate strikes. | `make sure the final files meet your expectations`, `resolve any remaining revisions`, `update the deliverable` | *(none)* |

### 2.5 Category E: Contextual Watchlist Words (`CONTEXTUAL` — `#3B82F6`)

| Trigger Word / Phrase | Why Fiverr Flags It | 3 Safe Replacements (`replacements[0..2]`) | Whitelisted Safe Contexts |
| :--- | :--- | :--- | :--- |
| `email` / `e-mail` / `gmail` / `outlook` / `yahoo mail` | Sharing personal email addresses is banned unless strictly needed for Gig execution inside the Order Page. | `Fiverr Inbox`, `the Order Requirements tab`, `message attachment` | `email marketing`, `email template`, `email copywriting`, `email newsletter`, `html email`, `cold email sequence` |
| `phone number` / `cell number` / `mobile number` / `call me` | Sharing personal phone numbers is prohibited. | `reach me here in the inbox`, `schedule a Fiverr Zoom call`, `drop a message here` | `call to action`, `api call`, `function call` |
| `zoom` | Allowed ONLY via Fiverr's official built-in Zoom button; external Zoom links are restricted. | `Fiverr's built-in Zoom feature`, `this message thread`, `a screen recording attachment` | `zoom in`, `zoom effect` |
| `contact details` / `personal contact` / `contact info` | Triggers the personal-information sharing warning banner. | `project requirements`, `account access details in the Order Page`, `brief details` | `contact form`, `contact us page` |
| `refund` / `cancel the order` / `chargeback` | High-risk dispute keywords; chargeback mentions can freeze accounts. | `mutual resolution`, `order adjustment`, `revision update` | *(none)* |

## 3. Regex & Anti-Obfuscation Engine (`src/lib/engine/obfuscation.ts`)

Fiverr sellers often try to bypass Fiverr's filter by typing `w h a t s a p p`, `p.a.y.p.a.l`, `wh@tsapp`, or `john [at] gmail [dot] com`. Fiverr's moderation AI specifically detects and penalizes deliberate obfuscation. Your scanner must detect these 5 regex patterns:

```typescript
export const REGEX_DETECTORS: RestrictedRule[] = [
  {
    id: 'regex-email-standard',
    pattern: '\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}\\b',
    isRegex: true,
    severity: 'CRITICAL',
    category: 'CONTACT_CREDENTIALS',
    reason: 'Direct email addresses are blocked in Fiverr Inbox. If an email is required for the service, ask the buyer to submit it inside the official Order Requirements page after ordering.',
    replacements: [
      '[share via the Order Requirements page]',
      '[provide inside the active Order Page]',
      'our Fiverr Inbox'
    ]
  },
  {
    id: 'regex-email-obfuscated',
    pattern: '\\b[A-Za-z0-9._%+-]+\\s*(?:\\[at\\]|\\(at\\)|\\bat\\b|@)\\s*[A-Za-z0-9.-]+\\s*(?:\\[dot\\]|\\(dot\\)|\\bdot\\b|\\.)\\s*(?:com|net|org|io|co)\\b',
    isRegex: true,
    severity: 'CRITICAL',
    category: 'EVASION_OBFUSCATION',
    reason: 'Obfuscated email addresses (using "at" / "dot") are flagged as deliberate ToS evasion and carry a high risk of account suspension.',
    replacements: [
      'the Order Requirements section',
      'this Fiverr Inbox chat',
      'our active Order Page'
    ]
  },
  {
    id: 'regex-phone-number',
    pattern: '(?:\\+?\\d{1,3}[\\s.-]?)?\\(?\\d{3}\\)?[\\s.-]?\\d{3}[\\s.-]?\\d{4}\\b',
    isRegex: true,
    severity: 'CRITICAL',
    category: 'CONTACT_CREDENTIALS',
    reason: 'Phone numbers are strictly prohibited in Fiverr messages.',
    replacements: [
      'this Fiverr chat',
      'Fiverr built-in Zoom',
      'the Order Page'
    ]
  },
  {
    id: 'regex-spaced-or-dotted-evasion',
    pattern: '\\b(?:w[\\s._-]*h[\\s._-]*a[\\s._-]*t[\\s._-]*s[\\s._-]*a[\\s._-]*p[\\s._-]*p|p[\\s._-]*a[\\s._-]*y[\\s._-]*p[\\s._-]*a[\\s._-]*l|t[\\s._-]*e[\\s._-]*l[\\s._-]*e[\\s._-]*g[\\s._-]*r[\\s._-]*a[\\s._-]*m|r[\\s._-]*e[\\s._-]*v[\\s._-]*i[\\s._-]*e[\\s._-]*w|e[\\s._-]*m[\\s._-]*a[\\s._-]*i[\\s._-]*l|p[\\s._-]*a[\\s._-]*y[\\s._-]*m[\\s._-]*e[\\s._-]*n[\\s._-]*t)\\b',
    isRegex: true,
    severity: 'CRITICAL',
    category: 'EVASION_OBFUSCATION',
    reason: 'Spacing or punctuating restricted words (e.g., "w h a t s a p p" or "p.a.y.p.a.l") is treated by Fiverr Trust & Safety as intentional filter evasion.',
    replacements: [
      'Fiverr Inbox',
      'a Fiverr Custom Offer',
      'check over the files'
    ]
  },
  {
    id: 'regex-leetspeak-evasion',
    pattern: '\\b(?:wh@tsapp|p4ypal|p@ypal|t3legram|em@il|r3view|p@yment|c@shapp)\\b',
    isRegex: true,
    severity: 'CRITICAL',
    category: 'EVASION_OBFUSCATION',
    reason: 'Symbol/leetspeak substitution (e.g., "wh@tsapp", "p@ypal") is automatically flagged by Fiverr moderation AI as deliberate policy evasion.',
    replacements: [
      'Fiverr Inbox',
      'a Fiverr Custom Offer',
      'inspect the delivery'
    ]
  }
];
```

## 4. Safety Score Formula (`src/lib/engine/scanner.ts`)

Compute the real-time **Fiverr Inbox Safety Score (0–100)** deterministically:

```typescript
export function calculateSafetyScore(matches: DetectedMatch[]): {
  score: number;
  statusLabel: ScanResult['statusLabel'];
} {
  let penalty = 0;
  for (const match of matches) {
    if (match.severity === 'CRITICAL') penalty += 35;
    else if (match.severity === 'HIGH') penalty += 20;
    else if (match.severity === 'CONTEXTUAL') penalty += 8;
  }
  const score = Math.max(0, 100 - penalty);

  const hasCritical = matches.some((m) => m.severity === 'CRITICAL');
  const hasHigh = matches.some((m) => m.severity === 'HIGH');

  if (hasCritical || score < 60) {
    return { score, statusLabel: 'Critical: Account Strike Risk' };
  }
  if (hasHigh || score < 90) {
    return { score, statusLabel: 'Caution: Warning Banner Risk' };
  }
  return { score, statusLabel: 'Safe to Send' };
}
```

## 5. Context-Aware AI Rewriter API Specification (`src/app/api/rewrite/route.ts`)

The AI Rewriter uses the [Vercel AI SDK](https://www.google.com/search?q=https%3A%2F%2Fai-sdk.dev%2Fdocs%2Fgetting-started%2Fnextjs-app-router) (`generateObject`) and [Zod](https://www.google.com/search?q=https%3A%2F%2Fzod.dev%2F) to produce **3 distinct, natural, 100% Fiverr-compliant variations** while enforcing a post-generation deterministic verification pass:

```typescript
import { NextResponse } from 'next/server';
import { generateObject } from 'ai';
import { createGoogleGenerativeAI } from '@ai-sdk/google';
import { createOpenAI } from '@ai-sdk/openai';
import { z } from 'zod';
import { scanMessage } from '@/lib/engine/scanner';
import { quickFixAllWithContext } from '@/lib/engine/ruleFixer';

const RewriteOutputSchema = z.object({
  variations: z.array(
    z.object({
      id: z.string(),
      tone: z.enum(['Natural & Polite', 'Professional & Concise', 'Client Boundary Redirect']),
      rewrittenText: z.string(),
      summaryOfChanges: z.string(),
    })
  ).length(3),
});

export async function POST(req: Request) {
  const { text, matches, customApiKey, provider = 'google' } = await req.json();

  // Check if an API key is available from either request header/body or environment
  const googleKey = customApiKey || process.env.GOOGLE_GENERATIVE_AI_API_KEY;
  const openaiKey = customApiKey || process.env.OPENAI_API_KEY;

  if (!googleKey && !openaiKey) {
    // Intelligent Local Hybrid Fallback (Zero API Key Required)
    const fallbackVariations = buildSmartLocalVariations(text, matches);
    return NextResponse.json({
      mode: 'hybrid-local',
      variations: fallbackVariations,
    });
  }

  const model = provider === 'openai' && openaiKey
    ? createOpenAI({ apiKey: openaiKey })('gpt-4o-mini')
    : createGoogleGenerativeAI({ apiKey: googleKey! })('gemini-2.5-flash');

  const systemPrompt = `You are an expert Fiverr Top Rated Seller communication coach and Trust & Safety compliance specialist.
Rewrite the seller's draft Fiverr inbox message into 3 natural, persuasive, 100% Fiverr-compliant variations:
1. "Natural & Polite": Preserves the seller's exact meaning and warm tone while smoothly replacing all restricted words.
2. "Professional & Concise": Crisp, executive-level phrasing suitable for Fiverr Pro buyers.
3. "Client Boundary Redirect": Politely keeps all communication, calls (via Fiverr's built-in Zoom), and payments (via Fiverr Custom Offers) strictly on Fiverr without sounding robotic or losing the sale.

STRICT PROHIBITIONS:
- NEVER output any of these restricted words: pay, payment, money, cash, fee, paypal, stripe, wise, payoneer, venmo, crypto, whatsapp, telegram, discord, skype, email, gmail, phone, call me, review, 5-star, rating, feedback, refund, outside fiverr.
- Replace "review" (when inspecting work) with "check over", "look through", or "inspect".
- Replace "pay/payment" with "confirm the Custom Offer", "proceed with the order", or "settle the milestone".
- Keep each variation under 2,400 characters.`;

  const { object } = await generateObject({
    model,
    schema: RewriteOutputSchema,
    system: systemPrompt,
    prompt: `Original Draft Message:\n"""\n${text}\n"""\n\nFlagged Triggers to Eliminate:\n${JSON.stringify(matches, null, 2)}`,
  });

  // Mandatory Post-Generation Deterministic Sanitization Gate
  const verifiedVariations = object.variations.map((v) => {
    const postScan = scanMessage(v.rewrittenText);
    const cleanText = postScan.matches.length > 0
      ? quickFixAllWithContext(v.rewrittenText, postScan.matches)
      : v.rewrittenText;
    return {
      ...v,
      rewrittenText: cleanText,
      safetyScore: scanMessage(cleanText).safetyScore,
    };
  });

  return NextResponse.json({
    mode: 'ai-cloud',
    variations: verifiedVariations,
  });
}
```
