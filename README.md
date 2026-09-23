# Schema Lab: an example microjournal publishing package

A minimal, working example of a single-author microjournal. Each article is
one text file. The package turns it into a web page, a matching PDF and an
entry in its issue, publishes the site on GitHub Pages, and can give each
article its own DOI through Zenodo.

The example content (two published articles, one draft, one issue) shows how
the parts fit together. Replace it with your own.

## Set up

1. Create a GitHub repository and upload the contents of this package.
2. **Settings → Pages → Source:** choose **GitHub Actions**.
3. **Settings → Actions → General → Workflow permissions:** choose
   **Read and write permissions**.
4. In `_config.yml`, set the publication title, tagline, description and
   homepage text, and the web address:
   ```yaml
   url: "https://USERNAME.github.io"
   baseurl: "/REPOSITORY"   # "" if the repository is USERNAME.github.io
   ```
5. Rewrite `about.md`.

The site then appears at `https://USERNAME.github.io/REPOSITORY/`. Every
commit to `main` rebuilds and republishes it within a few minutes; progress is
shown under **Actions**.

## How it works

The package has three kinds of content file.

**A schema** (`_data/schema-*.yml`) defines an article type: its sections, in
order. Each section needs a key, a label and a shape:

```yaml
version: 1
fields:
  - key: question
    label: "Question"
    shape: text          # text | list | table | boolean | date
    style: boxed         # optional: boxed | opinion | badge (default: plain)
  - key: bottom_line
    label: "Bottom line"
    shape: text
    teaser: true         # exactly one field: shown in listings
    max_chars: 300
```

Other options: `optional: true` for a section that may be left out,
`visibility: editor` for a private note that is never shown, and `columns`
for tables. The page and PDF templates read the schema rather than any section
name, so a new article type needs only a new schema file.
`_data/schema-brief.yml` is the fully annotated example.

**An article** (`_articles/*.md`) is front matter only. It has fixed base
fields, the name of its schema, a status, and one entry per schema section:

```yaml
---
title: "How long should a short article be?"
authors:
  - given: "Jane"
    family: "Example"
    affiliation: "Example Institute"
    orcid: "0000-0000-0000-0000"      # optional
corresponding_email: "jane@example.org"
volume: 1
issue: 1
order: 1
pages: "1-2"
published_date: 2026-09-01
licence: "CC BY 4.0"

schema: schema-brief
status: published                     # or draft

question: >-
  What length keeps a focused article useful?
bottom_line: >-
  One or two sentences that answer the question.
---
```

The file name becomes the web address (`/articles/FILE-NAME/`). Affiliations
and the correspondence email appear under the title; affiliations are
numbered automatically when authors have different ones. The DOI is added by
the Zenodo workflow; write a `doi:` line only for a DOI obtained elsewhere.

**An issue** (`_issues/v1-i1.md`) gives the volume, number and date, an
optional theme, and optional introduction text below the front matter. It
collects every article with the same volume and issue and
`status: published`, sorted by `order`.

**Status** decides what readers see. A draft is left out of every issue and
the homepage; a published article is included. The homepage features the
newest published article and lists the next four.

## Workflow

```
edit an article or schema ──► commit to main
                                   │
        "Validate, build and publish" (automatic)
          1. check every article against its schema
          2. lock the schema of any newly published article
          3. build the site and any new or changed PDFs
          4. publish to GitHub Pages
                                   │
        "Deposit article PDFs to Zenodo" (optional, started by hand)
          sandbox-test ─► test DOIs, labelled "not permanent"
          live         ─► permanent DOIs on the page and in the PDF
```

**Checks.** Step 1 stops the build, with nothing published, if a required
section is missing, a value has the wrong shape, the teaser is too long, or
a schema contains an unknown shape or style. The log names the file and the
problem.

**Schema locks.** When the first article using a schema is published, the
build records the schema's fingerprint in `_data/schema-locks.yml` and
commits it. After that, any change to the schema stops the build, so
published articles keep the structure they were published with. To change an
article type, copy it to a new file (`schema-brief-v2.yml`, `version: 2`) and
use that for new articles.

## DOIs (optional)

Only published articles are deposited, and an unchanged article is never
deposited twice.

1. **Test.** Create a token on [sandbox.zenodo.org](https://sandbox.zenodo.org)
   with the scopes `deposit:write` and `deposit:actions`. Save it as the
   repository secret `ZENODO_SANDBOX_TOKEN`. Run **Actions → Deposit article
   PDFs to Zenodo → Run workflow** with mode **sandbox-test**. After the
   automatic rebuild, each article shows a labelled test DOI.
2. **Go live.** Create a token on [zenodo.org](https://zenodo.org) with the
   same scopes and save it as the secret `ZENODO_TOKEN`. Run mode **live**.
   Permanent DOIs replace the test DOIs.
3. Mode **clear-test-dois** removes test DOIs without going live.

Set the repository variable `SITE_URL` (for example
`https://USERNAME.github.io/REPOSITORY`) so each Zenodo record links back to
its article.

## Everyday tasks

| Task | What to do |
| --- | --- |
| Write an article | Copy an example in `_articles/`, rename it, fill it in, keep `status: draft`. |
| Publish it | Change to `status: published` and commit. |
| Start an issue | Add `_issues/v1-i2.md`; give its articles `volume: 1`, `issue: 2`. |
| Add an article type | Add `_data/schema-NAME.yml`; use `schema: schema-NAME` in its articles. |
| Correct a published article | Publish a dated correction. A changed article gets a new DOI at the next live deposit. |
| Change text size | Edit `--text-scale` and `--heading-scale` at the top of `assets/css/style.css`. |

## Files

| Path | Purpose |
| --- | --- |
| `_config.yml` | Publication name, homepage text, web address |
| `about.md` | About page |
| `_articles/` | Articles |
| `_issues/` | Issues |
| `_data/schema-*.yml` | Article types |
| `_data/schema-locks.yml`, `_data/zenodo-ledger.yml` | Written automatically; do not edit |
| `_layouts/`, `_includes/`, `index.html`, `issues/` | Page templates |
| `assets/css/style.css` | Visual style |
| `scripts/validate_schema.py` | Checks and schema locks |
| `scripts/render_pdf.py`, `render_changed_pdfs.py`, `render_all_pdfs.py` | PDFs |
| `scripts/zenodo_articles.py` | Zenodo deposits |
| `.github/workflows/` | The two workflows |

## Local preview (optional)

```
pip install -r requirements.txt
python scripts/validate_schema.py
bundle install
bundle exec jekyll serve          # then open the address it prints
python scripts/render_pdf.py _articles/example-article-one.md example.pdf
```

## Licence

The code is MIT-licensed (see `LICENSE`). Each article carries the licence
stated in its own front matter.
