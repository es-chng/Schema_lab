---
# ---- Base fields: the same for every article ----
title: "When should a published article be corrected?"
authors:
  - given: "Jane"
    family: "Example"
    affiliation: "Example Institute"
    # orcid: "0000-0000-0000-0000"   # optional; adds an ORCID link
corresponding_email: "jane@example.org"
volume: 1
issue: 1
order: 2
pages: "3-4"
published_date: 2026-09-02
licence: "CC BY 4.0"
# doi: is added automatically by the Zenodo workflow; no need to write it

# ---- Article type (file in _data/) and whether it is live ----
schema: schema-brief
status: published               # draft = hidden from issues and the homepage

# ---- Schema fields: the sections defined in _data/schema-brief.yml ----
article_type: "Example brief"

question: >-
  Which kinds of error justify a dated correction rather than a silent
  edit, and how should the correction be shown?

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
  A second example article. Its teaser differs from the first so the
  homepage listing shows how each article is summarised.

conflicts_declared: false
last_reviewed: 2026-08-15

---
