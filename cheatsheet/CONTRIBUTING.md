# Maintaining the cheat sheet

For the teams that own a document, and for the QDC organizers.

## How it is organized

- `README.md` is the landing page. It holds every index of the cheat sheet and no facts of its own, and its views are generated.
- `reference/` has one document per tool type. Each names its owning team in its front matter.
- `guides/` holds what spans tools: getting started, techniques, testing and tips, and troubleshooting. The QDC organizers own the guides.
- `tools/taxonomy.toml` defines the tag values, the algorithm families, the task index of the landing page and the order of the documents. `tools/build_cheatsheet.py` checks the documents and rebuilds the generated sections.

## Rules

1. Each fact lives in one place. When another entry needs it, link to that entry instead of restating it.
2. Describe tools, not challenges: no challenge names, notebook variables, graded answers or organizer data.
3. Check every fact against PyPI, the release notes or the IBM Quantum documentation, and write dates in full, such as 16 October 2026.
4. Write for people and agents alike: short sentences with one idea each, exact API names in backticks, and links to the documentation.

## Entry format

An entry is a second-level heading (`##`) followed by a short description and then these fields, in this order. When to use and Tags are required; leave out any other field that does not apply. A `##` section without a Tags line is not an entry and appears in no view, and the build script reports a reference-document section that lacks one.

| Field | Content |
|---|---|
| `- **When to use:**` | One short clause that completes "Use it when", such as "you need …" or "before every hardware run". Put facts and comparisons in Watch out. |
| `- **Tags:**` | The entry's families, step, noise layer, where it runs, status and month of its newest change. |
| `- **Recent changes:**` or `- **Current version:**` | Dated changes from the last year, or the release the entry describes. |
| `- **Key APIs:**` | Import paths and signatures, in backticks. |
| `- **Watch out:**` | Pitfalls, one per bullet. |
| `- **Related:**` | Links to other entries. |
| `**Docs:**` | A list of links to documentation. Optional: leave it out when the page intro already points to the product documentation, as on the IBM Quantum Functions Catalog page. |

A Tags line looks like this:

```markdown
- **Tags:** family: trotter, floquet; step: post-process; noise: mitigation-expval; runs: local, qpu; status: stable; updated: 2026-09
```

- `family`: one or more algorithm families. `general` marks a tool that every family uses; add a family next to `general` when that family's workflows depend on the tool, so that it also appears in the family's view. The tools that a family's own entries tell readers to use are a good test.
- `step`: the workflow step, or steps, where the tool is used.
- `noise`: only for entries about suppressing, detecting, characterizing or mitigating noise.
- `runs`: where it runs, from `local` (no account) to `premium` (Premium, Flex or On-Prem plans only).
- `status`: the provider's label, exactly one of `stable`, `beta`, `alpha`, `experimental` and `preview`.
- `updated`: year and month of the newest change the entry records, in Recent changes, Current version or its text. Every entry with Recent changes needs it. An entry with neither Recent changes nor Current version must name that month in its text; the build checks this. The landing page's "New and changing" view is built from it.

`tools/taxonomy.toml` lists every allowed value with its meaning.

## Front matter and package tables

Every document starts with front matter:

```yaml
---
title: Qiskit SDK
short_title: Qiskit SDK
tool_type: sdk
owner: Qiskit SDK team
last_verified: 2026-10-20
---
```

`title`, `tool_type`, `owner` and `last_verified` are required. `short_title` is optional: it labels the document in the views, the All entries table and the versions table, and defaults to `title`.

A reference document that covers installable packages opens with a table headed `| Package | Version checked | Notes |`, before its first entry. The versions table in Getting started is built from these tables, so update a version in its own document only. Escape a `|` inside a cell as `\|`.

## Making a change

1. Edit the entry in its document. To add an entry, add a `##` section with a Tags line to the document of its tool type.
2. To add a task, a family or a tag value, edit `tools/taxonomy.toml`. A family, step or noise layer needs `id`, `title`, `short` and `description`; a status needs `id`, `title` and `description`; a task needs `text`, `links` (paths from this folder, usually with an anchor) and a `group`. List a new document in `documents.order`.
3. Run `python3 tools/build_cheatsheet.py` with Python 3.11 or later; it needs no packages. It rewrites the landing-page views and the versions table, then checks front matter, tags, required fields, field order, links, anchors, task links and repeated sentences.
4. When you re-check a document, update `last_verified` in its front matter. In a new month, also update `as_of` in `tools/taxonomy.toml`, which the "New and changing" view counts back from.
5. Commit the documents and the regenerated files together. `python3 tools/build_cheatsheet.py --check` changes nothing and fails when a check fails or a generated section is out of date. `tools/ci/cheat-sheet.yml` runs it on pull requests once you copy it to `.github/workflows/` and set a runner label your organization provides.

## Ownership

The owners named in front matter are proposals until the teams confirm them. Once their GitHub handles are known, a `CODEOWNERS` file at the repository root can route reviews:

```
/cheat_sheet_2026/reference/qiskit-sdk.md   @<sdk-team-handle>
/cheat_sheet_2026/guides/                   @<qdc-organizers-handle>
```
