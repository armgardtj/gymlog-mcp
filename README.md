# GymLog MCP server

GymLog is a strength-training log: "Letterboxd for lifting". Log your sets, follow your progress, and plan your
next session.

**This is a hosted MCP server, not open source.** GymLog runs it for GymLog users. This repository holds
documentation only: there is no server code here to install or run. You connect your AI assistant to the hosted
address below and sign in with your GymLog account.

```
https://getgymlog.web.app/mcp
```

The address may move to its own domain later. If it does, this address will keep working.

## Contents

- [What it does](#what-it-does)
- [Set up your assistant](#set-up-your-assistant)
  - [Claude](#claude)
  - [ChatGPT](#chatgpt)
  - [Grok](#grok)
  - [Other MCP clients](#other-mcp-clients)
- [Sign-in](#sign-in)
- [Permissions](#permissions)
- [Tools](#tools)
- [Limits](#limits)
- [Undo and disconnect](#undo-and-disconnect)
- [Privacy](#privacy)
- [Tested with](#tested-with)
- [Support](#support)

## What it does

Once connected, your assistant can read your GymLog training log: workouts, the history of any lift, personal
records, weekly to yearly totals, templates, your Up next plans and the brief GymLog shows at the start of a
workout (what you did last time and the weight to try today). If you allow it, it can also build templates, plan
sessions with target weights, log, edit or remove workouts, and import a training history it already holds.

Example: ask "What should I lift today?" and the assistant uses GymLog's own plan for the workout to answer.

The full tool reference is on the GymLog website: <https://getgymlog.web.app/mcp-docs>.
Setup help for people (not developers) is at <https://getgymlog.web.app/ai>.

## Set up your assistant

Menus change from time to time, and custom connectors may need a paid plan. If a step doesn't match, look for
"connectors" or "MCP" in your assistant's settings.

### Claude

1. In Claude on the web or the desktop app, open **Settings › Connectors** and choose **Add custom connector**.
2. Name it **GymLog** and paste the address: `https://getgymlog.web.app/mcp`
3. Choose **Connect**, sign in to GymLog and allow access.

Connectors you add on the web also work in the Claude mobile apps.

### ChatGPT

1. In ChatGPT on the web, open **Settings**, find the connectors (or apps) section and turn on **Developer mode**
   in its advanced settings.
2. Create a new connector, name it **GymLog**, paste the address and choose **OAuth** for sign-in:
   `https://getgymlog.web.app/mcp`
3. Sign in to GymLog and allow access, then turn GymLog on in a chat.

Custom connectors in ChatGPT depend on your plan and, for work accounts, your workspace settings.

### Grok

1. On grok.com, open **Connectors**, choose **New Connector**, then **Custom**.
2. Paste the address: `https://getgymlog.web.app/mcp`
3. Sign in to GymLog and allow access.

Availability depends on your Grok plan.

### Other MCP clients

| Setting   | Value |
|-----------|-------|
| Address   | `https://getgymlog.web.app/mcp` |
| Transport | Streamable HTTP |
| Sign-in   | OAuth 2.1 with PKCE. Your client registers itself (dynamic client registration or a client ID metadata document), so you don't need a client ID or secret. |
| Metadata  | `https://getgymlog.web.app/.well-known/oauth-protected-resource/mcp` |
| Protocol versions | `2026-07-28`, `2025-11-25`, `2025-06-18` |

The GymLog website lists steps for a few more clients at <https://getgymlog.web.app/ai#setup>. Those are not
covered under [Tested with](#tested-with).

### No GymLog account? Let your assistant create one

Add this address instead, with no sign-in:

```
https://getgymlog.web.app/mcp?signup=1
```

Your assistant asks you for the same things the GymLog app asks at sign-up (your email, the name others see, a
unique @handle, kg or lb, and whether you agree to the sign-up line: 18 or older, Terms of Service and Privacy
Policy) and creates the account only after your yes. GymLog then emails you a verification link: tapping it is the
only thing you do. From then on every tool works in the same chat, with no reconnecting. Until you tap the link,
only the three sign-up tools work; the others answer "sign up and verify your email first" and do nothing. If
nobody taps the link within 3 days, GymLog deletes the account.

## Sign-in

GymLog uses OAuth. When you connect, your assistant opens GymLog's connect page, where you:

1. Sign in with the account you use in the GymLog app (Google, Apple or email), or create a GymLog account on the
   same page. The app is optional.
2. See which assistant is asking and choose what it may do (see [Permissions](#permissions)).
3. Tap **Allow**.

Nothing is shared until you tap Allow. The assistant gets an access key that works for 1 hour and renews it with a
renewal key. A connection the assistant hasn't renewed for 60 days ends on its own.

If you already use the GymLog app, turn on **Sync across devices** so your assistant sees those workouts. Workouts
kept only on your phone are not visible to it.

## Permissions

You choose these on the connect page. Seeing your training is always part of connecting; each kind of change can
be switched off, and **Read only** switches off all of them.

| Scope | In plain words |
|-------|----------------|
| `training:read` | See your workouts, exercise history, records, templates and plans |
| `plans:write` | Create and change your templates and plans, and set targets for your next sessions |
| `workouts:write` | Log workouts for you, and edit or remove your workouts |

## Tools

25 tools once you are signed in, plus 3 sign-up tools on the sign-up address. "Read" tools change nothing.
Every change tool takes a `clientRequestId`, so a retried request happens only once.

### Read (`training:read`)

| Tool | Read or change | What it does |
|------|----------------|--------------|
| `get_profile` | Read | Your GymLog basics: units (kg or lb), whether you use the phone app, how many workouts you have and when your phone last synced. |
| `list_workouts` | Read | Your workouts, newest first, with an optional date range. |
| `get_workout` | Read | One workout in full: every exercise and set. |
| `get_exercise_history` | Read | One exercise over time: sets, top set, best estimated 1RM, volume, its progression settings and GymLog's Stalled / Plateau verdict. |
| `get_personal_records` | Read | Personal records per exercise: heaviest set, best estimated 1RM, most reps and biggest volume. |
| `get_training_summary` | Read | Totals for the week, month, quarter or year containing a date. |
| `list_templates` | Read | Your workout templates, when each was last run, and their exercises. |
| `list_programs` | Read | GymLog's built-in program library. |
| `search_exercises` | Read | Finds exercises in GymLog's library and your own custom exercises by name or alias. |
| `get_exercise_guide` | Read | How to perform an exercise: steps, muscles worked, equipment type and demonstration photos (with their credit line). |
| `list_equipment` | Read | Your custom equipment and your home gym's plate set. Never gym names or locations. |
| `list_planned_sessions` | Read | Your Up next queue: planned sessions with target weights and reps. |
| `get_todays_plan` | Read | The brief GymLog shows at the start of a workout, word for word: what you did last time, what to do today, warm-ups and plates. Returns today's plan for a template or planned session. |
| `request_account_deletion` | Change (sends an email only) | Asks GymLog to email you a link to delete your account. It deletes nothing: only you can, from that link. At most one link a minute, 3 a day. |

### Plans (`plans:write`)

| Tool | Read or change | What it does |
|------|----------------|--------------|
| `create_template` | Change | Creates a workout template: a name and its exercises with sets and reps. |
| `update_template` | Change | Renames a template or replaces its exercise list. |
| `remove_template` | Change | Removes a template from every device. Past workouts are not touched. |
| `update_exercise_settings` | Change | Changes how GymLog progresses one exercise: weight step, auto-progress, warm-ups or weight rule. |
| `plan_session` | Change | Puts a session in your Up next list with target weights and reps for each set. |
| `remove_planned_session` | Change | Takes one planned session out of Up next. |

### Workouts (`workouts:write`)

| Tool | Read or change | What it does |
|------|----------------|--------------|
| `log_workout` | Change | Adds a finished workout. It is private and never posted to friends. |
| `update_workout` | Change | Changes one of your workouts: date, time, duration, title, exercises or sets. |
| `remove_workout` | Change | Removes one of your workouts from every device. The assistant must say which one and get your OK. |
| `import_workouts` | Change | Moves a training history the assistant already holds into GymLog. Always a dry run first, with a summary to show you. Duplicates are skipped. |
| `remove_import` | Change | Removes every workout of one import in one step. |

### Sign-up (only on `https://getgymlog.web.app/mcp?signup=1`, no sign-in)

| Tool | Read or change | What it does |
|------|----------------|--------------|
| `get_started` | Read | Explains how to create a GymLog account from the chat. |
| `create_account` | Change | Creates a GymLog account after you agree, and emails you a verification link. |
| `check_account_status` | Read | Shows whether you have tapped the link yet; once you have, the same chat is signed in. |

Arguments and full descriptions for every tool: <https://getgymlog.web.app/mcp-docs>.

## Limits

Per assistant you connect, on your account:

- 60 calls an hour (20 of them changes) and 600 calls a day.
- Imports have their own limit: 120 import calls and 2 new imports a day, 50 workouts a call, 3,000 workouts an
  import, 50 new custom exercises an import.
- Free accounts can log or import workouts dated today and the 30 days before. Older workouts need GymLog Pro,
  which is sold in the GymLog app. Older rows in an import come back skipped, not added.
- Assistant sign-up: 10 new accounts a day per network, 3 attempts a day per email address.

Connecting an assistant is free. No GymLog MCP tool runs AI on GymLog's side.

## Undo and disconnect

Everything an assistant adds or changes shows its name in GymLog. In the GymLog app, go to
**Settings › Connected apps** and pick the assistant:

- **Undo its changes** puts back what that assistant added, changed or removed in the last 30 days, except
  anything you changed yourself since. The undo history stays for the 30 days even after you disconnect.
- **Undo import** removes a whole import in one step (the assistant can also do this with `remove_import`).
- **Disconnect** stops its access at once. It does not delete your GymLog account.

Removing GymLog inside the assistant may not tell GymLog, so disconnect in GymLog to be sure. No app? Email us
(the address is in the privacy policy) or delete your account on the GymLog website, which disconnects every
assistant.

## Privacy

Full policy: <https://getgymlog.web.app/privacy.html> (Section 3a covers AI assistants).

- **What it can see:** your workouts (dates, times, durations, exercises, sets, reps, weights, effort, set types,
  cardio), the history, records and summaries worked out from them, your templates, exercise settings, custom
  exercises and equipment, your home gym's plate set (weights and counts only), your Up next plans and your units.
- **What it never sees:** your notes, gyms or any location, photos, sharing settings, feed, friends, comments or
  reviews, your name, @handle or email, purchases, Apple Health or Health Connect data, or workouts and sets you
  logged for someone else.
- **What it can't do:** share anything, change your friends, profile or account, or delete your account.
- **What GymLog keeps:** the connection (assistant name, what you allowed, when connected and last used, keys in
  hashed form only); a record of each request with no training data in it, kept 90 days; the undo history for 30
  days; and request counters for the limits.
- **The assistant's company:** what your assistant reads goes to the company that runs it, under that company's
  own privacy policy, and it may keep it after you disconnect. GymLog doesn't use what an assistant sends it for
  advertising, profiling or AI training.
- **Deletion:** disconnecting deletes GymLog's request records for that assistant. Deleting your GymLog account,
  in the app or on the website, deletes your data and disconnects every assistant.

This is a training log, not medical advice.

## Tested with

These were run by the GymLog team against the GymLog server code (on GymLog's test server, which runs the same
code as the address above):

- **MCP Inspector CLI 2.10.1** with an access key: `tools/list` and tool calls.
- **A scripted Streamable HTTP client:** the full OAuth flow (client registration, sign-in, consent with each
  permission, code exchange, key renewal, revoke) and `tools/list` on both the current and older protocol versions;
  every read tool on an account with a few hundred synced workouts, including `get_todays_plan` for every template;
  the plan and workout change tools, a dry-run-then-commit import, `remove_import`, Undo and Disconnect; and the
  assistant sign-up flow from `create_account` to a signed-in session, then `request_account_deletion`.
- **Grok (grok.com):** a real sign-up connection on `?signup=1`. This test is why the sign-up connection now lists
  every tool from the start, for assistants that don't refresh their tool list.

The Claude and ChatGPT steps above are the ones on the GymLog website; they are not yet listed here as tested.

## Support

Email us: the address is in the [privacy policy](https://getgymlog.web.app/privacy.html) (Section 12,
Contact). Setup help: <https://getgymlog.web.app/ai>. Tool reference: <https://getgymlog.web.app/mcp-docs>.
