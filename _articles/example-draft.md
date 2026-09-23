---
# ---- Base fields: the same for every article ----
title: "A draft: not yet in any issue"
authors:
  - given: "Jane"
    family: "Example"
    affiliation: "Example Institute"
    # orcid: "0000-0000-0000-0000"   # optional; adds an ORCID link
corresponding_email: "jane@example.org"
volume: 1
issue: 1
order: 3
pages: "5-6"
published_date: 2026-09-01
licence: "CC BY 4.0"
# doi: is added automatically by the Zenodo workflow; no need to write it

# ---- Article type (file in _data/) and whether it is live ----
schema: schema-brief
status: draft                   # change to published when ready

# ---- Schema fields: the sections defined in _data/schema-brief.yml ----
article_type: "Example brief"

question: >-
  What length keeps a focused practical article useful without leaving out
  what the reader needs?

background: >-
  This is placeholder text showing a plain text field. Replace it with your
  own content. Long text can be written over several lines in the source file;
  it is joined into one paragraph on the page and in the PDF.

evidence:
  - source: "Example source A (2025)"
    finding: "Placeholder finding for the first row of the table."
    limitation: "Placeholder limitation."
  - source: "Example source B (2024)"
    finding: "Placeholder finding for the second row."
    limitation: "Placeholder limitation."

recommendations:
  - "A list field: each item becomes one bullet."
  - "Boxed style draws rules above and below the section."

open_questions:
  - "An optional field. Delete it and the section simply disappears."

perspective: >-
  The opinion style sets this section apart as the author's own view,
  distinct from the evidence above.

bottom_line: >-
  This article is a draft, so it appears in no issue and not on the
  homepage. Set status to published to include it.

conflicts_declared: false
last_reviewed: 2026-08-15

editorial_note: "Editor-only field: never shown on the site or in the PDF."
---
