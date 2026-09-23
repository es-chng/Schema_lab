# Schema Lab: an example microjournal publishing package

A minimal, working example of a single-author microjournal. You write each
article as one text file; the package turns it into a web page, a matching
PDF and an entry in its issue, publishes the site on GitHub Pages, and can
give each article its own DOI through Zenodo.

The example content (two published articles, one draft, one issue) is only
there to show how everything fits together. Replace it with your own.

## Set up (once)

1. Create a GitHub repository and upload the contents of this package.
2. **Settings → Pages → Source:** choose **GitHub Actions**.
3. **Settings → Actions → General → Workflow permissions:** choose
   **Read and write permissions**.
4. Edit `_config.yml`: publication title, tagline, description, homepage
   text, and `url` (your `https://USERNAME.github.io`).
5. Edit `about.md`.

Every change you commit to `main` rebuilds and republishes the site within a
few minutes (see the **Actions** tab).

## How it works

**Articles** (`_articles/*.md`) contain only front matter, in two parts:

- *Base fields*, the same for every article: title, authors (each with an
  optional affiliation and ORCID), corresponding email, volume, issue, order,
  pages, published date and licence. The DOI is added automatically by the
  Zenodo workflow; write a `doi:` line only for a DOI obtained elsewhere. Affiliations and the correspondence
  email appear under the title on the page and in the PDF; affiliations are
  numbered automatically when authors have different ones.
- *Schema fields*: the sections defined by the article's schema.

**A schema** (`_data/schema-*.yml`) lists the sections an article type
contains, in order. Each section needs only a `key`, a `label` and a
**shape** (`text`, `list`, `table`, `boolean`, `date`). Add a **style**
(`boxed`, `opinion`, `badge`) only to change the default plain look, and
`visibility: editor` only for private notes that are never shown. Exactly one
section is the **teaser** shown in listings. An article names its schema by
file (`schema: schema-brief`). The templates read the schema, never individual section
names, so a new article type needs only a new schema file.
`_data/schema-brief.yml` is the annotated example.

**An issue** (`_issues/v1-i1.md`) gives the volume, number, date, an optional
theme, and optional introduction text below the front matter. It collects
every article with the same `volume` and `issue` and `status: published`,
sorted by `order`.

**Status** controls visibility: `status: draft` keeps an article out of every
issue and off the homepage; `status: published` includes it.

## The workflow

```
write or edit an article  ──►  commit to main
                                   │
             "Validate, build and publish" runs automatically:
               1. check every article against its schema
                  (missing sections, wrong shapes, teaser too long)
               2. lock the schema of any newly published article
               3. build the site and the PDFs (only changed PDFs are rebuilt)
               4. publish to GitHub Pages
                                   │
             optional, started by hand: "Deposit article PDFs to Zenodo"
               sandbox-test ─► test DOIs, shown on the site as "not permanent"
               live         ─► real DOIs; page and PDF updated automatically
```

If step 1 finds a problem, nothing is published and the log names the
article, the section and the problem.

**Schema locks.** When the first article using a schema is published, the
schema is locked (recorded in `_data/schema-locks.yml`, committed by the
build). A locked schema cannot be changed, so published articles always keep
their structure. To change an article type, copy the schema to a new file
such as `schema-brief-v2.yml` and use it for new articles.

## DOIs (optional)

Only articles with `status: published` receive a DOI; an unchanged article is
never deposited twice.

1. **Test first.** Create a token on [sandbox.zenodo.org](https://sandbox.zenodo.org)
   (scopes `deposit:write`, `deposit:actions`) and save it as the repository
   secret `ZENODO_SANDBOX_TOKEN`. Run **Actions → Deposit article PDFs to
   Zenodo → Run workflow** with mode **sandbox-test**. The site rebuilds and
   each article shows a labelled test DOI.
2. **Go live.** Create a token on [zenodo.org](https://zenodo.org) with the
   same scopes, save it as the secret `ZENODO_TOKEN`, and run mode **live**.
   Real DOIs replace the test DOIs. Live DOIs are permanent.
3. Mode **clear-test-dois** removes test DOIs without going live.

Optionally set the repository variable `SITE_URL` (your site's address) so
each Zenodo record links back to its article page.

## Everyday tasks

| Task | What to do |
| --- | --- |
| Add an article | Copy an example from `_articles/`, rename it (the file name becomes the web address), fill it in, keep `status: draft` until ready. |
| Publish it | Change to `status: published` and commit. |
| Start a new issue | Add `_issues/v1-i2.md`; set `volume: 1`, `issue: 2` in its articles. |
| New article type | Add a new `_data/schema-NAME.yml`; set `schema: schema-NAME` in the article. |
| Correct a published article | Publish a dated correction; a changed article receives a new DOI on the next live deposit. |
| Change text size | Edit `--text-scale` and `--heading-scale` at the top of `assets/css/style.css`. |

## Files

| Path | Purpose |
| --- | --- |
| `_config.yml` | Publication name and homepage text |
| `about.md` | About page |
| `_articles/` | Articles |
| `_issues/` | Issues |
| `_data/schema-*.yml` | Article types (schemas) |
| `_data/schema-locks.yml`, `_data/zenodo-ledger.yml` | Written automatically; do not edit |
| `_layouts/`, `_includes/`, `index.html`, `issues/` | Page templates |
| `assets/css/style.css` | Visual style |
| `scripts/validate_schema.py` | Checks articles and manages schema locks |
| `scripts/render_pdf.py`, `render_changed_pdfs.py`, `render_all_pdfs.py` | PDF generation |
| `scripts/zenodo_articles.py` | Zenodo deposits |
| `.github/workflows/` | The two workflows above |

## Local preview (optional)

```
pip install -r requirements.txt
python scripts/validate_schema.py
bundle install && bundle exec jekyll serve
python scripts/render_pdf.py _articles/example-article-one.md example.pdf
```

## Licence

Code: MIT (see `LICENSE`). Articles you publish carry the licence stated in
their own front matter.
