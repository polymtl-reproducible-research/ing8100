# ING8100 — Science ouverte et recherche reproductible

Course administration files for **ING8100** at Polytechnique Montréal.

Lecture material lives separately, in the course book:
<https://reproducible-research.polymtl.ca> · [source](https://github.com/polymtl-reproducible-research/myst_book)

---

## Contents

```
plan-de-cours/
├── ING8100_plan_de_cours.tex          # source of the syllabus
├── Makefile                           # local build
├── nicematrix.sty                     # pinned — see Notes
├── Logo_Polytechnique_Montreal_noir.jpg
└── Symbole_Polytechnique_Montreal_noir_rgb.jpg
```

---

## Updating the plan de cours for a new term

The whole cycle, start to finish.

### 1. Edit the source

```bash
cd plan-de-cours
$EDITOR ING8100_plan_de_cours.tex
```

Update at minimum: the term (`\session`), the calendar dates, the evaluation
weightings and dates, and the week in which the teaching evaluation takes place
(15 minutes of class time — required by CONSAC since June 2025).

### 2. Build locally and check it

```bash
make
open ING8100_plan_de_cours.pdf
```

Read the PDF before going further. Local building exists for exactly this: CI
will tell you whether it *compiles*, not whether the dates are right.

### 3. Commit and push

```bash
cd ..
git add -A
git commit -m "Plan de cours, <term>"
git push
```

CI compiles the document again on the way in. A green check means the committed
source builds cleanly from scratch, not just on your machine.

### 4. Tag a release

This is what publishes the PDF at a permanent address.

```bash
git tag -a a2026 -m "Plan de cours, automne 2026"
git push origin a2026
```

Tag names follow the Poly convention: `a2026` for autumn, `h2027` for winter.
If a correction is needed mid-term, bump the tag (`a2026.1`) rather than moving
the existing one — the previous version stays reachable, which is the point.

### 5. Put the link on Moodle

```
https://github.com/polymtl-reproducible-research/ing8100/releases/download/a2026/ING8100_plan_de_cours.pdf
```

Substitute your tag. Use the **pinned tag** on Moodle so students always see the
version that was in force when they enrolled, and a later correction cannot
silently change the syllabus they were shown.

For the course website, where you would rather not edit a link every year:

```
https://github.com/polymtl-reproducible-research/ing8100/releases/latest/download/ING8100_plan_de_cours.pdf
```

Both require the repository to be **public**. On a private repository they prompt
for authentication and are useless as course links.

---

## Building locally

### Prerequisites

A LaTeX distribution providing **`pdflatex`**, from **TeX Live 2024 or later**.
Earlier releases fail — see Notes.

- **macOS, full** — [MacTeX](https://tug.org/mactex/) (`brew install --cask mactex`)
  includes everything needed.
- **macOS, BasicTeX** — the minimal distribution omits several packages this
  template uses. After installing it:

  ```bash
  sudo tlmgr update --self
  sudo tlmgr install changepage fancyhdr parskip babel-french hyperref
  ```

  If a build stops on `File 'xxx.sty' not found`, install `xxx` the same way —
  the missing filename is the package name.
- **Linux** — `sudo apt install texlive-latex-recommended texlive-latex-extra texlive-lang-french`,
  provided your distribution ships TeX Live 2024 or later.

`latexmk` is used when available; otherwise the Makefile falls back to two
`pdflatex` passes, so it is optional.

### Commands

```bash
cd plan-de-cours
make          # produces ING8100_plan_de_cours.pdf
make watch    # rebuild on every save (needs latexmk)
make clean
```

---

## Building on push (CI)

**The PDF is never committed.** It is produced by GitHub Actions, so the
published document always corresponds to a known commit of the source.

### What triggers a build

`.github/workflows/plan-de-cours.yml` runs on every push or pull request
touching `plan-de-cours/`, and can be started by hand from the Actions tab
(**Plan de cours** → *Run workflow*). It compiles in a container carrying a full
TeX Live, which is why CI never hits the missing-package problems a local
BasicTeX install does.

### Where the PDF ends up

| Trigger | Result | Where | Lasts |
|---|---|---|---|
| Any push, PR, or manual run | Build artefact | Actions → the run → **Summary** page, bottom, *Artifacts* | 90 days, sign-in required |
| A pushed **tag** | Release asset | Releases, with a permanent URL | Indefinitely, public |

Artefacts appear on a run's **Summary** page, not on the job log page — a
recurring source of confusion. Use them to confirm a build compiled; use a
release to distribute the document.

On an untagged run, the *Attach the PDF to the release* step shows as skipped.
That is correct: it is gated on the ref being a tag.

---

## Why this is in Git rather than in a shared drive

The syllabus is revised every year. Under version control, next year's revision
is a diff against this one; in a shared folder it is a file called
`plan_cours_v3_final.tex`. Given what this course teaches, keeping its own
administration reproducible seemed like the minimum.

The CI workflow is also a live example used in class: the PDF students read was
produced by an automated build whose log they can inspect.

---

## Notes

**`nicematrix.sty` is pinned deliberately — do not delete it.** The Polytechnique
template ships its own copy, and it is load-bearing in both directions:

- Recent TeX Live versions ship a `nicematrix` that requires the `color-inside`
  key to use `\rowcolor` outside `\CodeBefore`. The template does not use that
  key, so the *installed* version fails where the pinned one succeeds.
- The pinned copy in turn needs a LaTeX kernel from **TeX Live 2024 or later**.
  On TeX Live 2023 it aborts with `Your LaTeX release is too old`.

So the build needs the pinned `.sty` *and* a current TeX Live. If you ever see
either of those two error messages, that combination is what to check.

**Logos.** Two Polytechnique brand images sit next to the `.tex` because the
template includes them by bare filename. They are institutional visual identity,
not openly licensed material — if this repository is public, confirm with the
communications department that redistributing them is acceptable, or remove them
and document where staff obtain them.

**Status.** The `.tex` still contains `[À COMPLÉTER]` markers for information not
yet fixed — evaluation weightings, dates, office and extension, and the list of
guest contributors. Find them with:

```bash
grep -n "À COMPLÉTER\|À VALIDER" plan-de-cours/ING8100_plan_de_cours.tex
```

---

## Licence

Course material is released under CC-BY 4.0 unless stated otherwise. The
Polytechnique Montréal template and logos are excluded from that grant.
