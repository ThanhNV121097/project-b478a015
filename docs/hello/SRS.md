# SRS — Hello Word

Module: `hello`
Last updated: 2026-05-27
Design: [View the approved design](design/index.html)
Design system: `design/design-system.md`

> One file per module, at `docs/{module}/SRS.md`. It covers only the functions
> that belong to this module. Never write `docs/SRS.md`.

## 1. Purpose

The `hello` module is the entire product: a single screen that displays the
text "Hello Word" (the stakeholder's own wording) centered on a white
background with dark text and no motion. The text is not hardcoded in the
frontend — it is fetched from a PostgreSQL database through the Go backend, so
the words shown are data, not decoration. Without this module there is no
website.

## 2. Actors

| Actor | Who they are | What they may do in this module |
|---|---|---|
| Guest | Any visitor to the site, not signed in | Load the single page and read the displayed text |

There is no sign-in, no authoring, and no admin role. Every visitor is a Guest
with identical access.

## 3. Scope

**In scope** — the functions specified below, by their plan titles:

- Display Hello Word from database

**Out of scope** — named so a reader does not look for it here:

- Editing or creating the displayed text — there is no UI for it; the text
  lives in the database and is seeded by the backend.
- Authentication and user accounts — a public single screen needs none.
- Analytics, tracking, or cookies — the page is static content only.

## 4. Functional requirements

### 4.1 Display Hello Word from database

**Requirement HELLO-001 — Fetch and display the message**

*As a* Guest, *I want to* load the page and see the text "Hello Word" centered
on the screen, *so that* I know the site works and reads its content from the
database.

Behaviour:

1. The Guest opens the site root URL.
2. The frontend requests the message from the backend.
3. The backend reads the message from the PostgreSQL database.
4. The backend returns the message text to the frontend.
5. The frontend renders the message centered horizontally and vertically on a
   white background with dark text.
6. The page applies no animation and no motion to the message.

**Requirement HELLO-002 — Loading state**

*As a* Guest, *I want to* see a visible loading indication while the message is
being fetched, *so that* I am not looking at a blank screen and wondering if
the page is broken.

Behaviour:

1. Before the backend responds, the page shows a loading state in place of the
   message.
2. When the response arrives, the loading state is replaced by the loaded
   message.

**Requirement HELLO-003 — Loaded state**

*As a* Guest, *I want to* see exactly the text stored in the database, *so
that* the displayed words match the data rather than a hardcoded string.

Behaviour:

1. When the backend returns a non-empty message, the frontend renders that
   exact text, verbatim, centered on the screen.

**Requirement HELLO-004 — Empty state**

*As a* Guest, *I want to* see a clear indication when the database has no
message stored, *so that* a missing row is not mistaken for a working page or a
blank screen.

Behaviour:

1. When the backend reports that no message row exists, the frontend renders an
   empty state: the fixed placeholder text "No message" in the same centered
   position and styling as the loaded message.

**Requirement HELLO-005 — Error state**

*As a* Guest, *I want to* see an error message with a way to retry when the
message cannot be loaded, *so that* a backend or database failure is reported
and recoverable rather than silently blank.

Behaviour:

1. When the backend is unreachable, returns an error, or the database read
   fails, the frontend renders an error state in place of the message.
2. The error state shows the message "Could not load the message" and a retry
   control labelled "Retry".
3. Activating the retry control re-runs the fetch from step 1 of HELLO-001.

**Acceptance criteria** — each maps one-to-one onto a test case in
`docs/hello/test-cases/display-hello-word.md`.

| # | Given | When | Then |
|---|---|---|---|
| AC-1 | The database holds a message "Hello Word" | The Guest loads the site root | The page shows the text "Hello Word", centered horizontally and vertically |
| AC-2 | The database holds a message "Hello Word" | The Guest loads the site root | The displayed text is rendered in dark text on a white background |
| AC-3 | The fetch is in progress | The Guest loads the site root | A loading indication is visible in place of the message, and disappears once the response arrives |
| AC-4 | The database holds the message "Bonjour" | The Guest loads the site root | The page shows "Bonjour" — proving the text comes from the database, not a hardcoded string |
| AC-5 | The database holds no message row | The Guest loads the site root | The page shows the empty-state placeholder text "No message" instead of a blank screen |
| AC-6 | The backend is unreachable | The Guest loads the site root | The page shows the error message "Could not load the message" with a retry control labelled "Retry" |
| AC-7 | A previous load failed and the error state is showing | The Guest activates the retry control "Retry" | The page re-fetches and shows the loaded message once the backend recovers |
| AC-8 | The message is displayed | The Guest observes the message | No animation, transition, or motion is applied to the message |

**Failure, boundary and permission behaviour**

| Case | Condition | Expected behaviour |
|---|---|---|
| Empty data | No message row exists in the database | Empty state shown (HELLO-004); not a blank screen |
| Upstream failure | Backend unreachable or returns an error | Error state with a retry control (HELLO-005); the page does not crash |
| Slow response | Backend takes a long time to respond | Loading state remains visible until the response arrives or fails |
| Permission | None needed | The page is public; every Guest sees the same content with no auth check |
| Invalid content | Message row exists but text is null or whitespace-only | Treated as the empty state (HELLO-004), not rendered as blank |
| Conflict | Not applicable | The module has no writes, so no concurrent-edit conflict exists |

**Data touched** — the fields this function reads, in product terms. The
physical schema is TL's job in `docs/architecture/erd.md`; this list is what
that document has to satisfy.

| Field | Type | Required | Rule |
|---|---|---|---|
| message | text | yes | The exact string to display, e.g. "Hello Word". May not be null or empty; whitespace-only is treated as empty. |

The module reads only this one field and writes nothing.

## 5. Screens

The design is the source of truth for appearance; this section maps the
functions onto it so nothing in the design is unaccounted for.

| Screen | Section in the design | Functions it serves | States that must exist |
|---|---|---|---|
| Hello Word | Single centered screen | HELLO-001, HELLO-002, HELLO-003, HELLO-004, HELLO-005 | loading, loaded, empty, error |

The design shows one screen: the text "Hello Word" centered on a white
background, with four distinct states — loading, loaded, empty, and error — as
recorded in the approved design spec.

## 6. Non-functional requirements

| Area | Requirement |
|---|---|
| Performance | The page renders the message within 2 seconds once the backend responds |
| Accessibility | The message text is real text (selectable, exposed to screen readers), not an image |
| Responsive | The centered message wraps without horizontal page scroll at 320px width and up |
| Localisation | Copy is in English; the message itself renders verbatim as stored, whatever language it is stored in |
| Privacy | No personal data is collected or stored; the page requires no cookies |

## 7. Dependencies and assumptions

- **Depends on:** the `hello` backend endpoint and the PostgreSQL database for
  the stored message text.
- **Depends on:** the approved design (`design/index.html`) for the exact
  appearance of the four states.
- **Assumption:** the message text is seeded into the database by the backend
  at startup or migration. If that is not done, the site shows the empty state
  until a row exists. The stakeholder's requested text is "Hello Word" (not
  "Hello World") and is quoted verbatim.

The following copy is pinned and used verbatim throughout the requirements and
acceptance criteria:

| Copy | Pinned text |
|---|---|
| Empty state | "No message" |
| Error state | "Could not load the message" |
| Retry control | "Retry" |

## 8. Traceability

| Plan item | Requirement ids | Test cases |
|---|---|---|
| Display Hello Word from database | HELLO-001, HELLO-002, HELLO-003, HELLO-004, HELLO-005 | `test-cases/display-hello-word.md` |
