# Asking questions on AgentConnect: elicitation cards

AgentConnect renders a runtime's **structured question** (ACP `elicitation/create`,
form mode) as a card in the conversation — a webchat card, a Slack Block Kit card — with
real controls (radio options, checkboxes, a text or number input, a multi-field form) and
a submit. The answer comes back as typed data, not as a free-text message you have to
parse. Use this for every parameter in the create-agent flow.

## How to raise a card

Use your runtime's structured-question tool — in Claude Code that is
`AskUserQuestion`; other ACP runtimes expose an equivalent "ask the user" request.
The daemon turns it into the platform card. Plain prose questions still work but
cost the user a typed reply and cost you a parsing step; reserve prose for
explanations, not for collecting values.

## What a card can hold

| Field kind   | Renders as                           | Notes                                                              |
| ------------ | ------------------------------------ | ------------------------------------------------------------------ |
| `enum`       | single-choice options                | Give each option a short label; the value is what you get back.    |
| `multi-enum` | checkboxes + confirm                 | Supports min/max item counts.                                      |
| `boolean`    | two options (yes/no)                 | Use for confirmations such as "Create this agent?".                |
| `text`       | free-text input                      | Can carry `minLength`/`maxLength`/`pattern`/format (email, uri…).  |
| `number`     | numeric input                        | Optional integer flag and min/max.                                 |

- **Defaults** are honored: the card pre-fills the control, the user may change it.
- **Multi-field form**: one card can carry 2–**10** fields with one submit. Above
  10 fields the daemon declines the card — split it.
- **Slack** shows at most **5 option buttons** per choice; webchat has no cap.
  When an enum has more than 5 options and the user might be in Slack, split the
  list or ask the long list as a text field with the options spelled out.
- Options are the **only** answers the user can give; there is no "other" unless
  you add one. Always add an escape hatch such as "Something else" / "Skip" where
  the list might be incomplete.
- A **dismissed / declined** card is a real answer: stop and ask how the user wants
  to proceed instead of retrying the same card.
- **URL mode** exists for consent flows (open this link, e.g. a GitHub App install
  page). Use it when a prerequisite requires the user to complete a flow in their
  browser: show the exact URL, wait for them to come back, then re-check.

## Where cards do not work

Headless sessions — cron firings, inbound hooks (GitHub / webhook), dream and other
daemon-internal passes — have nobody to answer, and the Claude Code runtime has
`AskUserQuestion` disabled there. If this skill is invoked from such a session, say
the flow needs an interactive chat (webchat or a chat platform) and stop.

## Rules of thumb for this skill

1. **Read before you ask.** Option lists (daemons, runtimes, models, efforts,
   permission modes, existing agent names) come from the admin tools, never from
   memory. Stale or invented values fail at `createAgent`.
2. **One topic per card.** Placement in one card, behavior in the next, identity in
   the third, confirmation last. A user should be able to accept most cards as-is
   thanks to good defaults.
3. **Label with consequences.** "Permission mode: bypass (edits files without
   asking)" beats "bypass". Put the human name (`name`) and the runtime's own
   description in the label when the catalog provides them.
4. **Confirm before writing.** Every create/update goes through a boolean card that
   restates what will be created. The confirmation is the user's decision, not a
   formality — respect a "no".
5. **Never collect secrets** in a card or in chat: no tokens, no API keys, no
   passwords. Prerequisites that need a credential are done by the user in the
   console or on the provider's site; you only re-check afterwards.
