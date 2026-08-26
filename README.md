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

## Building the plan de cours

### Prerequisites

A LaTeX distribution providing **`pdflatex`**, from **TeX Live 2024 or later**,
including `babel-french` and `nicematrix`. Earlier releases fail — see Notes.

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

### Build

```bash
cd plan-de-cours
make          # produces ING8100_plan_de_cours.pdf
make watch    # rebuild on every save (needs latexmk)
make clean
```

**The PDF is not committed.** It is built by CI on every push
(`.github/workflows/plan-de-cours.yml`) and attached to the run as an artefact.
Tagging a commit publishes the PDF as a release asset — that is the version to
upload to Moodle, so the file students receive is always traceable to a specific
commit.

```bash
git tag -a a2026 -m "Plan de cours, automne 2026"
git push origin a2026
```

## Why this is in Git rather than in a shared drive

The syllabus is revised every year. Under version control, next year's revision
is a diff against this one; in a shared folder it is a file called
`plan_cours_v3_final.tex`. Given what this course teaches, keeping its own
administration reproducible seemed like the minimum.

The CI workflow is also a live example used in class: the PDF students read was
produced by an automated build whose log they can inspect.

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
template includes them by bare filename. They are institutional visual identity, not openly licensed
material — if this repository is made public, confirm with the communications
department that redistributing them is acceptable, or remove them and document
where staff obtain them.

**Status.** The `.tex` still contains `[À COMPLÉTER]` markers for information not
yet fixed — evaluation weightings, dates, office and extension, and the list of
guest contributors. Find them with:

```bash
grep -n "À COMPLÉTER\|À VALIDER" plan-de-cours/ING8100_plan_de_cours.tex
```

## Licence

Course material is released under CC-BY 4.0 unless stated otherwise. The
Polytechnique Montréal template and logos are excluded from that grant.
