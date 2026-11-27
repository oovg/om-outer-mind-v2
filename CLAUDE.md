# OM outer mind: instructions for Claude

This repository is The Open Machine's **outer mind**: everything here is public or about to be. The sites (hub, media shop, wiki and research notebooks) are built only from `public/`.

You work **after** the airlock. A person has already cleaned, screened and approved every item in `landing/`. You never see, and must never ask for, the inner mind.

## Hard rules

1. **Read and write only inside this repository.** Do not open any other folder, even if a tool can reach it.
2. **Never add facts.** Use only what is in the landed item and what already exists in `public/`. If a required field can't be filled from those, leave it empty, put `# TODO` beside it, and list it in your report.
3. **OM's own words are verbatim.** Notebooks, sections, concepts, project indexes, pages, quarterly intros and site settings are published exactly as written: don't rewrite, reorder, shorten or "improve" them. You place them and fill fields. Transmissions and events are also verbatim in their body; you only write their `summary`.
4. **Never re-add removed text.** Plain-text words that were links stay plain text unless the target now exists in `public/`.
5. **Don't commit, push or switch branches.** Leave your changes in the working tree. The person reviews them and ships them with `om ship`, which commits them, opens pull requests or pushes, and redeploys the sites.
6. **Content only, never presentation.** Write plain markdown and fields. No HTML tags, `class` or `style` attributes, colours, sizes, alignment or layout hints, even if a landed note seems to want them (report it instead). How things look is decided in `om-sites`.
7. Don't touch landed items you are not processing, and never edit `CLAUDE.md` or `schemas.md`.

## Folder structure

```
public/                       what the sites read; every type is defined in schemas.md
  site.md
  pages/<site>/  updates/  projects/  media/  events/  concepts/
  (media/ holds transmissions, collections and shop entries side by side; `type` tells them apart)
  notebooks/<notebook>/       index.md + one file per section
  assets/
landing/<slug>/               arrived through the airlock: note.md, assets/, handoff.json; you add receipt.json
landing/.done/<slug>/         filed by om land once the receipt exists: receipt.json only
suggestions/notebooks.md      your suggestions (not read by any site)
```

## What each landed note becomes

`handoff.json` gives you:

- `kind`: `publish`, `update` or `retract`;
- `route`: what the note is, decided by its folder in the inner mind (always set);
- `framework`: for notebooks, `true` if it's a framework;
- `mediashop`: for transmissions and collections, `true` if it also goes in the media shop;
- `previous_entries`: for updates and retractions, the entries made from this note before. If it's empty, find them yourself: first the `entries` in `landing/.done/<slug>/receipt.json` (the receipt from this note's last landing), then any entry in `public/` with the same slug and its `-edition` shop item. Say in the report where you found them.

**The route decides; you don't.** Follow this table exactly:

| `route.type` | Make |
|---|---|
| `notebook` | `public/notebooks/<route.notebook>/index.md`, type `notebook`, with `framework` from the handoff |
| `notebook-section` | `public/notebooks/<route.notebook>/<route.section>.md`, type `section`, with `notebook: <route.notebook>`; then update the notebook's `sections` |
| `concept` | `public/concepts/<slug>.md` |
| `project` | `public/projects/<route.project>.md`, with `status` from the note. The airlock has already reduced it to description, `## Outputs` and `## Updates`: keep all three exactly; rewrite output links to final slugs, and drop a link whose target isn't in `public/` |
| `transmission` | `public/media/<slug>.md`, type `transmission`; plus a media entry **only if `mediashop` is `true`**. If `route.collection` is set, it's an essay in that book: set `collection: <route.collection>` and title it `route.essay` |
| `collection` | `public/media/<route.collection>.md`, type `collection`, with `essays`; plus **one** media entry for the whole book only if `mediashop` is `true` |
| `event` | `public/events/<slug>.md` |
| `page` | `public/pages/<route.site>/<slug>.md`, with `site` and `path` from the route (slug = the path's last part, or `home` for `/`) |
| `quarterly` | `public/updates/<yyyy>-q<n>.md`, with `site` and `quarter` from the route; then build its digest |
| `site` | Replace `public/site.md` |

## Making each type

Follow `schemas.md` for fields. For every entry:

- Keep the landing slug unless it collides or reads badly. Routed slugs like `notebook-…`, `project-…` or `hub-…` are internal: use the path from the table above.
- If the note has `summary` (the airlock sets it from the note's `summary` or `description`, or for a project from its description), keep it exactly. Otherwise write one or two plain sentences drawn from the text. Every entry except pages and settings must have one.
- Take `date` (and `updated`, if present) from the note. The airlock always sets `date`; never invent one. If it's somehow missing, leave the field out and report the entry.
- Assets are already in `public/assets/`: `om land` copies every landed item's assets there before you start (their names are content hashes). Point to them as `assets/<name>`.
- Every `[[slug|text]]` must exist in `public/`. Landed notes link with their landing slugs: rewrite those to the final slug, or make them plain text if the target isn't published.

**Notebooks.** The main note's links to its sections give the reading order: set `sections` to the published sections in that order, then any others alphabetically. When a section lands later, add it to `sections` in the right place. Keep `framework` exactly as handed off. Link to concepts only where the note itself links them.

**Collections (books).** The book's index note gives its name (already in `title`) and its table of contents: set `essays` to the published pieces in table-of-contents order, then any pieces with `collection:` set to this book that the contents don't list (by date). Report those, so the person can add them to the contents. When an essay lands later, add it to `essays` in its place, and if the book has a media edition, nothing else is needed: the build regenerates it. Essays never get their own media entry or `in_mediashop`; the book does. An essay whose book isn't published yet keeps `collection:` and is reported, so the book can follow.

**Concepts.** Fill in:
- `related`: concepts that already exist in `public/concepts/` and that the note names or links;
- `sources`: entries in `public/` that link to this concept.

When a new concept relates to an existing one, add the new slug to the existing concept's `related` list. That's the only edit you make to another entry for concepts.

**Projects and `project:`.** A transmission or event may carry `project:` as a name or a `[[link]]` to a project index (e.g. `[[Extitutions index]]`). Store it as the project's slug, which must exist in `public/projects/`; otherwise set `project: # TODO` and report it. When an entry clearly belongs to a project but has no `project:`, don't add one: suggest it in your report. Many media pieces were made without a project; if several untagged pieces clearly share one, suggest creating a project for them.

**Pages.** Keep every `##` heading and its text exactly, including slot headings, with or without intro text (see `schemas.md`). Two pages can't share a site and path: report a clash rather than resolving it.

**Site settings.** Turn the note's lists into `site.md`'s fields (`sites`, `nav`, `contact`, `licence`, `tagline`, `notebooks_footer`) exactly as written. A nav item pointing to a hub page uses `path`, another site uses `to`, and an external link uses `url`.

**Media shop** (only when `mediashop` is `true`):
- Make one media entry: `public/media/<slug>-edition.md`, type `media`, with `edition_of: <transmission or collection slug>` and no body.
- **Move** the note's `cover`, `files` and `formats` (if present) from the note onto the media entry, unchanged; they point at custom files in `assets/`. The transmission or collection never carries them.
- Also **move** `role`, `descriptor` and `licence` onto the media entry, and **copy** `subtitle` and `author` there (the transmission or collection keeps those two). Give the media entry the note's `title`, `date` and `summary`.
- Don't make, find or substitute files yourself: `pnpm media` in `om-sites` generates whatever is missing.
- Set `in_mediashop: <media slug>` on the transmission or collection.
- Never add anything to the shop when `mediashop` isn't `true`. If a piece seems worth it, say so in your report.

**Updates.** Edit each of `previous_entries` in place with the new text. Keep their slugs and any fields the new note doesn't change. If `mediashop` changed, add or remove the media entry and `in_mediashop` to match (to remove one, list it under `remove` in the receipt). If `cover`, `files` or `formats` changed, update the media entry to match (removing any the note no longer has). If `framework` changed, update it.

**Retractions.** A retraction is the person's own instruction, given by setting `publish: false`: carry it out without asking. List each of `previous_entries` (found as above if the list is empty) in the receipt (`"retracted": true`), including a shop item made from it; `om land` deletes them. Remove or unlink any links to them in other entries, including `related`, `sources`, a notebook's `sections` and a book's `essays`.

## Quarterly digests

When a `quarterly` lands, or comes back as an update, build its digest from `public/` only:

1. **Collect** everything dated inside the calendar quarter (Q1 = Jan–Mar): transmissions, media, events, concepts, notebooks and sections, plus every project `## Updates` line dated in the quarter.
2. **Group** by project, using `project:` on entries and the update lines' own project. Anything without a project goes under "Other", last.
3. **Write** it below the intro, starting with a line that marks the generated part:

```markdown
<the person's intro, verbatim>

<!-- digest: generated from public/ on 2026-10-02; edit the intro above, not below -->

## Extitutions
*Status: active*
- 2026-07-14: First salon in Istanbul. *(project update)*
- [[istanbul-salon|Istanbul Salon]]: event, Istanbul, 14 July
- [[intelligence-is-immanent|Intelligence is Immanent]]: transmission, 23 October

## Other
- …
```

Rules:
- One line per item: a link, its type, and a short plain fact taken from its fields. Never interpret or praise.
- Mark status changes (update lines containing `Status →`) in bold.
- Rebuild the whole digest on every update, so it reflects `public/` as it is now.

## After each item

- Check the entry against `schemas.md`. (`om build` runs the sites' full check afterwards.)
- Write `landing/<slug>/receipt.json`, listing the main entry first:
  ```json
  { "entries": ["public/media/noopunk.md", "public/media/noopunk-edition.md"], "notes": "mediashop: true, so an edition was added." }
  ```
  For a retraction: `{ "retracted": true, "entries": ["public/media/old-post.md"] }`. To delete an entry in an update (e.g. a shop item when `mediashop` became false), add `"remove": ["public/media/x-edition.md"]`.
- Don't move or delete anything yourself: you have no shell. Once the receipt exists, `om land` deletes what the receipt says, files the item into `landing/.done/`, and cleans it up.

## Suggestions

When `landing/` is empty, rewrite `suggestions/notebooks.md` from scratch, based only on what is in `public/`:

```markdown
# Suggestions (<date>)

## New notebooks worth starting
### <Proposed title>
- Core concepts: [[concept-a]], [[concept-b]], [[concept-c]]
- Drawn from: [[essay-x]], [[event-y]]
- Why now: one or two sentences on why these concepts belong together.

## Notebooks with new terrain
### <notebook>
- New or changed concepts it doesn't cover yet: [[concept-d]]
- Where it would extend: which section, in a sentence.

## Frameworks
- <n> published. Working notebooks that look stable enough to consider as frameworks: [[notebook]] (one line why).

## Terms without a concept entry
- "<term>": used in [[entry]], [[entry]]. Worth a concept note?

## Untagged work
- [[media-piece]]: looks like part of [[project]] (or: several pieces suggest a new project, "<name>").
```

Suggest a new notebook only when at least three concepts clearly belong together. Keep it short. These are prompts for a person, not plans.

## Report

When done, give the person:
- one line per item: slug → entries made, any TODO fields, and anything you were unsure about;
- for a quarterly, the digest's item count per project;
- a one-line pointer to `suggestions/notebooks.md` if it changed.

They will preview and ship. Your receipts are only read on their machine by the airlock's own code, which checks them before it changes anything; you never write to the inner mind.
