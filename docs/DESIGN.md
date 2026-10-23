# UI & UX rules

Our users are shop owners, marketers and agencies, many on phones, many more comfortable in
Bangla than English. If a screen needs a tutorial to understand, the screen is wrong.

The visual and behavioral reference is `docs/mockup/index.html`. Build to match it.

## 1. Words

- Use the words customers use, not system words: "Auto-reply", not "autonomy level";
  "people reached", not "impressions"; "Problem: video too long", not "Error 400".
- Every page opens with a title and **one sentence saying what the page is for**.
- Buttons say exactly what happens: "Send reply", "Schedule posts", "Pause everything".
- Explain every AI decision in one plain line: what it thinks, how sure it is, and why
  ("Wants to buy · 96% sure · Asked about price and size").
- Errors say what went wrong and how to fix it. No apologies, no codes.

## 2. Layout and flow

- **One main action per page**, as the only filled (primary) button in the header.
- Long tasks are steps (Create post = Idea → Check → When). Show the steps; allow going back.
- Start pages with what needs the user ("Needs you"), then results, then details.
- New users see a setup guide on Home until it is done or hidden.
- Empty states say what will appear and give the button to make it appear.

## 3. Phones first

- Under 860px: bottom tab bar (Home, Inbox, Create, Calendar, More). No sidebar.
- Touch targets at least 42px tall. No horizontal page scrolling; grids wrap.
- Toasts and sheets sit above the tab bar and respect safe areas.

## 4. Language and local details

- Full Bangla and English interface, switchable at any time; remember the choice.
- Bangla text uses Hind Siliguri. Content keeps its own language regardless of UI language.
- Money in Taka with Bangladeshi grouping: ৳1,84,500. Times in Bangladesh time, 12-hour.
- Weeks start on Saturday.

## 5. Safety in the interface

- Prefer **Undo** over "Are you sure?" dialogs. Every send, pause, rule change and hide offers Undo.
- Anything the AI did is visible and labeled ("Answered by AI · 11:42 AM").
- The "Pause everything" control is always one tap away (top bar pill → Auto-reply page).
- Locked options are shown disabled with the reason, never hidden.
- Platform limits are explained where they matter ("TikTok doesn't allow comment replies from apps").

## 6. Accessibility

- Text contrast meets WCAG AA in light and dark themes.
- Every control works with a keyboard and shows a visible focus ring.
- State is never shown by color alone: add a word or icon (chip text, check marks).
- Respect reduced-motion settings.
