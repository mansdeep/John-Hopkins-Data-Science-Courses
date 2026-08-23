# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Course materials for the Johns Hopkins Data Science Specialization on Coursera
(https://www.coursera.org/specialization/jhudatascience/1), contributed by Brian Caffo, Jeff Leek,
Roger Peng, Nick Carchedi, and Sean Kross. Licensed under CC-NC-SA
(http://www.tldrlegal.com/l/CC-NC-SA). Materials are lecture slide decks, not application code —
there is no build/test/lint tooling to run.

## Organization

Top-level folders are the 9 courses in the specialization, in order:

- `01_DataScientistToolbox`
- `02_RProgramming`
- `03_GettingData`
- `04_ExploratoryAnalysis`
- `05_ReproducibleResearch`
- `06_StatisticalInference`
- `07_RegressionModels`
- `08_PracticalMachineLearning`
- `09_DevelopingDataProducts`

Within each course folder, every lesson lives in its own subfolder (e.g.
`07_RegressionModels/01_05_linearRegression`, `08_PracticalMachineLearning/010caretPackage`,
`09_DevelopingDataProducts/shiny`) and is an independent, self-contained slide deck — there is no
shared build script tying lessons together. Some courses also carry alternate/legacy content
directories worth knowing about so you don't mistake them for the canonical deck: e.g.
`06_StatisticalInference/rmd/` and `06_StatisticalInference/old_markdown/` hold earlier drafts of
lessons alongside the current per-lesson folders, and `07_RegressionModels/originalContent/`
holds an earlier pass at that course's decks.

Shared, top-level support directories:

- `assets/` — images, CSS, JS, and layouts shared across all decks (referenced by decks as
  `../../assets`).
- `libraries/` and `librariesNew/` — two generations of the slidify framework runtime
  (frameworks/highlighters/widgets JS+CSS) that decks pull in via their YAML front matter's
  `url.lib` path. Older decks point at `../../libraries`; current decks point at
  `../../librariesNew`. Both must stay in the repo since old decks still resolve against the old
  one.
- `zips/` — a few pre-packaged course archives (not all courses have one).
- `dependencyGraph.R` — present but currently empty.

## Authoring/build pattern

Each lesson is authored with **slidify** (R Markdown → HTML slide deck), following a fixed
three-file pattern per lesson folder:

- `index.Rmd` — the source. YAML front matter declares `framework` (usually `io2012`),
  `highlighter`, `hitheme`, and a `url:` block pointing `lib` at `../../libraries` or
  `../../librariesNew` and `assets` at `../../assets`. Slide bodies are Markdown separated by
  `---`, with R code chunks (knitr) for plots/output.
- `index.md` — the knitted intermediate Markdown, produced by running the `.Rmd` through knitr.
- `index.html` — the final self-contained slide deck (`mode: selfcontained`), produced by
  slidify from `index.md`. This is the file actually served/viewed.

Lessons with code chunks that render figures typically also have a `fig/` or `figure/` subfolder
of generated PNGs, and some (e.g. under `08_PracticalMachineLearning`) have a knitr `cache/`
directory of cached computation. These are generated artifacts, not source — if a slide's R code
or content changes, `fig/`, `figure/`, and `cache/` need to be regenerated, not hand-edited.

To rebuild a deck locally you'd need R with the `slidify` and `slidifyLibraries` (or equivalent
bundled libraries) and `knitr` packages installed, then run something like
`slidify::slidify("index.Rmd")` from within the lesson folder. There is no repo-wide script that
does this for all lessons — `dependencyGraph.R` exists but is empty, so don't assume it drives a
build.

## Deployment

The repo has a `.nojekyll` file at the root and committed `index.html` output alongside every
`index.Rmd`. This means the site is served directly from committed HTML via GitHub Pages, with no
CI build step — `.nojekyll` just tells Pages to serve the raw files (including the underscore-led
`librariesNew` etc.) without running its Jekyll processor. Practically: if you edit an `index.Rmd`,
you must also regenerate and commit the corresponding `index.md`/`index.html` (and any
`fig`/`figure`/`cache` artifacts) for the change to actually show up on the published site —
editing the `.Rmd` alone has no effect on what's served.

## Known repo quirks

- `07_RegressionModels/01_01_introduction/` contains leftover unresolved git merge-conflict
  artifacts (`index.Rmd.BACKUP.943.Rmd`, `.BASE.943.Rmd`, `.LOCAL.943.Rmd`, `.REMOTE.943.Rmd`)
  alongside the real `index.Rmd`. Don't treat these as content to edit or as evidence of an
  in-progress conflict — `index.Rmd` is the canonical source there.
- Scattered `.Rhistory` files sit inside lesson folders (leftover R console history from
  authoring); they aren't part of the deck content.
