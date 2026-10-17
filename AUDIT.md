# ESL Teacher Tools — generator audit (17 Oct 2026)

Branch: `fix/generator-audit-oct26`. Production file: **`index.html`** (latest commit on `main` renamed `index-template.html` → `index.html`). The other HTML files are older copies and were not changed.

## 1. Fixed in this branch (index.html only)

| Issue | Evidence before | Fix |
|---|---|---|
| Flashcards showed the same 8 generic words (appointment, information…) for every topic, and every sentence repeated the topic name ("I have an appointment about Catching the Train.") | Headless test: identical words for Transport, Health and Shopping; 8/8 sentences contained the topic | Added `TOPIC_VOCAB`: 8 topic-specific words + natural sentence + picture for all 60 topics. Lesson Plan "Key words" line and Beginner vocabulary list now use the same words, so the tabs match. |
| Printed reflection sheet lost the teacher's notes | PDF showed a 2-line clipped strip of the textarea, no headings | Textarea is prefilled with the headings; notes are mirrored into a print-only block; print/printable view/copy all include the full text. |
| Reflection notes only saved on blur, and one note was shared across every topic | Typing then refreshing lost notes; new topic showed old topic's notes | Autosave while typing (400 ms) + on blur + on page hide; notes stored per topic; old single-key notes are migrated once, not lost. Storage errors show a warning instead of failing silently. |
| Long tabs (Lesson Plan, Helper Sheet) printed only page 1 and cut off mid-sentence | Lesson Plan PDF ended at "End with a"; Helper Sheet lost "DO NOT" and debrief sections | Print CSS now keeps content in normal flow (was `position: fixed`). Browser's own Ctrl+P now prints the active tab instead of a blank page. |
| Title said "Template Version"; no description/social metadata; no favicon; header badge "Template version"; footer "no API key required" | — | New title, meta description, Open Graph tags, canonical, theme colour, inline favicon; badge "Free to try"; plain-English privacy footer. |
| Upgrade copy typo "Unlimited lesson generates" | — | "Unlimited lesson generation". |
| Payhip buttons open a store branded **SpeakLevel** with no warning | — | Links unchanged. Added one line under the options: "Lesson pack PDFs are sold through our SpeakLevel store on Payhip. App access is paid securely through Stripe." |

Preserved: mixed-level tabs (A1/B1/B2/Conversation), Helper Teacher Sheet text, flashcard 4×2 landscape print and Word export, all Payhip and Stripe URLs, the free-limit behaviour.

## 2. Free-generation limit — not secure

The limit is a counter in `localStorage` (`eslplanner_gen_count`, `FREE_LIMIT = 1`). Anyone can bypass it by opening a private window, clearing site data, using another browser, or visiting `?testmode=true`, which shows a public "Reset Free Trial Counter" button. Treat it as a **friendly nudge, not access control**.

**Critical commercial gap:** the Monthly (A$19) and Annual (A$97) Stripe buttons sell "unlimited access", but nothing in the code unlocks the app after payment — there is no login, licence key or server check. A paying customer would still hit the same limit. This was **not** changed because it needs a decision and real infrastructure. Options, cheapest first:
1. Pause the two Stripe buttons (or relabel as "early access — we'll email your access link") until access exists.
2. Stripe Payment Link → success page with a shared unlock code (still bypassable; honest only if described as such).
3. Real accounts: e.g. Memberstack/Outseta/Supabase auth + Stripe webhook. Only this is genuine paid access.

## 3. Secrets

Scanned all 5 HTML files for API keys, tokens and passwords (Anthropic, Stripe secret, Google, GitHub patterns): **none found**. The Stripe `buy.stripe.com` links are public Payment Links and safe to publish. Note: the old comments describe reconnecting the Claude API by putting a key in the browser — don't; an API key in public HTML/JS is exposed to every visitor. Use a server-side function if AI generation returns.

## 4. Other risks

- If the site serves the repo root, `index-pricechange.html`, `fixedflashcard.html` and the two backups are probably publicly reachable with old pricing and old flashcards. Not verified (hosting provider unknown). Recommend moving them to an `archive/` branch or deleting them from `main`.
- No `og:image` yet — add a 1200×630 preview image for better link sharing.
- Google Fonts is loaded from Google's servers (fine; mention in a privacy note if you add one).
