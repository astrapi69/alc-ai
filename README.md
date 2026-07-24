# alc-ai

[![content validation](https://github.com/astrapi69/alc-ai/actions/workflows/validate-content.yml/badge.svg)](https://github.com/astrapi69/alc-ai/actions/workflows/validate-content.yml)
[![engine on npm](https://img.shields.io/npm/v/learn-content-engine?label=engine%20on%20npm)](https://www.npmjs.com/package/learn-content-engine)

The [Adaptive Learner](https://github.com/astrapi69/adaptive-learner)
content repository for **KI** (artificial intelligence): a Git repository
of plain lesson files that the app loads directly and no vendor can lock
away.

It ships two German-language knowledge sets (domain `ai`, `domain_label`
KI) in ascending difficulty: the companion course to the book *KI für
Einsteiger* and the follow-up course on data science. This repository
was created from
[adaptive-learner-content-template](https://github.com/astrapi69/adaptive-learner-content-template),
which provides the schema mirror, validator, CI and authoring tooling
described below.

> **Herkunft:** Diese Sets lagen zuvor im offiziellen Content-Repo
> [`adaptive-learner-content`](https://github.com/astrapi69/adaptive-learner-content)
> und wurden in dieses eigenständige Content-Repo verschoben (siehe
> `adaptive-learner-content#144`).

## Die Sets

Zwei Sets, 21 Lektionen, Quell- und Zielsprache Deutsch. Empfohlene
Reihenfolge:

### Teil 1 — `sets/de/ki-einsteiger` (A1, 12 Lektionen)

Begleitkurs zum Buch *KI für Einsteiger* (Asterios Raptis) — siehe
[`books.yaml`](books.yaml). KI-validiert (`ai_validation`).

| # | Lesson | Titel |
|---|--------|-------|
| 01 | `01-ki-im-alltag.json` | KI im Alltag |
| 02 | `02-ki-grundbegriffe.json` | KI-Grundbegriffe |
| 03 | `03-prompting-grundlagen.json` | Prompting Grundlagen |
| 04 | `04-prompt-typen.json` | Prompt-Typen |
| 05 | `05-ki-projekte-planen.json` | KI-Projekte planen |
| 06 | `06-ki-projekte-kreativ.json` | KI-Projekte kreativ |
| 07 | `07-ki-lernen.json` | Mit KI lernen |
| 08 | `08-ki-kreativitaet.json` | KI und Kreativität |
| 09 | `09-ki-produktivitaet.json` | KI und Produktivität |
| 10 | `10-ki-teamarbeit.json` | KI im Team |
| 11 | `11-ki-tools-ueberblick.json` | KI-Tools Überblick |
| 12 | `12-ki-ethik-verantwortung.json` | KI-Ethik und Verantwortung |

### Teil 2 — `sets/de/data-science-ki` (A2, 9 Lektionen)

| # | Lesson | Titel |
|---|--------|-------|
| 01 | `01-was-ist-data-science.json` | Was ist Data Science? |
| 02 | `02-daten-verstehen.json` | Daten verstehen |
| 03 | `03-statistik-grundlagen.json` | Statistik für den Alltag |
| 04 | `04-ml-grundkonzepte.json` | Maschinelles Lernen: Grundkonzepte |
| 05 | `05-modelle-bewerten.json` | Modelle bewerten |
| 06 | `06-neuronale-netze.json` | Neuronale Netze und Deep Learning |
| 07 | `07-sprachmodelle-generative-ki.json` | Sprachmodelle und generative KI |
| 08 | `08-ki-im-einsatz.json` | KI im Einsatz |
| 09 | `09-verantwortung.json` | Verantwortung |

Buchempfehlungen pro Domain stehen in [`books.yaml`](books.yaml),
ergänzendes freies Material (Videos, Artikel) in
[`media.yaml`](media.yaml).

## What's inside

- `manifest.yaml` — the root manifest listing the sets.
- `sets/de/ki-einsteiger/`, `sets/de/data-science-ki/` — the lesson sets.
- `books.yaml` / `media.yaml` — recommended reading and free media per domain.
- `schema/` — the pinned [`learn-content-engine`](https://github.com/astrapi69/learn-content-engine)
  schema mirror; [`engine-version.txt`](schema/engine-version.txt) holds the
  pinned engine version and is the source of truth. This is what the content
  is validated against — independent of the app.
- `templates/` — starting-point lessons per domain (language / programming / knowledge).
- `scripts/validate_content.py` — the local validator.
- `scripts/generate_exercises.py` — an optional BYOK AI exercise generator.
- `generated/` — staging area for AI drafts (never shipped directly).
- `.github/workflows/` — CI that validates every push/PR against the pinned engine.
- `docs/` — [GETTING-STARTED.md](docs/GETTING-STARTED.md) and a local
  [LESSON-FORMAT.md](docs/LESSON-FORMAT.md). The **canonical, test-validated**
  format reference is the engine's
  [`docs/lesson-format.md`](https://github.com/astrapi69/learn-content-engine/blob/main/docs/lesson-format.md).

## Quick start

You only need `make` and `python3`. The first `make validate` sets up a
local environment for you (no manual `pip`, no virtualenv, no Poetry):

```bash
git clone https://github.com/astrapi69/alc-ai.git
cd alc-ai

# Validate the sets. First run creates .venv and installs deps;
# later runs reuse it. Exit 0 == all sets pass.
make validate
```

Before you push, `make lint` runs the same semantic engine gate as CI
(`Engine conformance`): it installs the engine release pinned in
`schema/engine-version.txt` into `node_modules/` (gitignored; needs Node.js
and npm) and checks every lesson and manifest with the engine's rule ids
(`E-CARD-REF` & co.). `make lint-warnings` additionally prints the engine gate's warnings (`W-*`).

No `make` (e.g. Windows without WSL)? Two options: run the validator in a
virtualenv yourself —

```bash
python3 -m venv .venv && . .venv/bin/activate     # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python3 scripts/validate_content.py
```

— or just commit and let the GitHub Actions CI validate (it runs the same
checks). Installing the deps globally with a bare `pip install` fails on
modern Debian/Ubuntu/macOS (PEP 668, "externally-managed-environment");
the virtualenv above is why.

Full walkthrough: [docs/GETTING-STARTED.md](docs/GETTING-STARTED.md).

## Export a set for AI review

`scripts/export_set.py` writes all lessons of ONE set into a single
YAML (or JSON) file so an AI assistant or a human can review the whole
set in one pass (syntax, correctness, consistency across lessons):

```bash
python3 scripts/export_set.py ki-einsteiger
# -> exports/ki-einsteiger-de-<timestamp>.yaml
python3 scripts/export_set.py ki-einsteiger --format json --out /tmp/review.json
```

The slug is the set id from the root `manifest.yaml`
(`ki-einsteiger-from-de`) or the folder name of the set path
(`ki-einsteiger`); when the same folder name exists under several
source-language directories, `--lang` (default `de`) picks the
`sets/<lang>/` directory. Non-ASCII characters stay real UTF-8. An
unknown slug aborts with a list of the available sets.

The export is self-contained: its first field `review_instructions`
holds the complete review prompt from
[`docs/ai-review-prompt-template.md`](docs/ai-review-prompt-template.md)
(read at runtime, not copied into the script). The export file can be
handed to a review AI as-is, without manually prepending a prompt. Edit
the review instructions in that template file and keep the sibling
content repos in sync.

**Read-only snapshot, NOT a re-import format:** nothing reads the
export back. Changes flow only through the individual schema-validated
lesson JSONs under `sets/`. The `exports/` folder is gitignored.

Full usage guide and best practices (incl. the source-chapter workflow):
[`docs/export-set-usage.md`](docs/export-set-usage.md) (English) /
[`docs/export-set-usage.de.md`](docs/export-set-usage.de.md) (Deutsch).

## Export a graded quiz to PDF (school tests)

`scripts/export_quiz_pdf.py` turns a lesson that carries a graded-quiz
exercise (an `ext:*-graded-quiz`: a scored question set, points per
question, optional partial credit on multi-select, an optional
percentage pass threshold) into two print-ready PDFs:

```bash
python3 scripts/export_quiz_pdf.py path/to/graded-quiz.json --out-dir out/
# -> out/<id>-test.pdf      (question paper for students, no answers)
# -> out/<id>-loesung.pdf   (answer sheet for the teacher)
```

The test paper shows the questions with blank checkboxes / answer lines
and the points; the answer sheet shows the correct answers, the points,
a partial-credit note, and the pass threshold. This is a consumer tool -
it renders one presentation of a canonical lesson and does not invoke the
engine, so it is independent of the pinned engine version.

**Caveat (adaptive-learner-content-test#66):** graded-quiz content uses
the `ext:` extension tier, which the content gate (`make lint`) does not
yet accept (it validates core-only and refuses ext lessons). Until that
adoption lands, keep graded-quiz lessons OUTSIDE `sets/` and run the tool
on them directly (a runnable sample lives in
[`tests/fixtures/graded-quiz-sample.json`](tests/fixtures/graded-quiz-sample.json)).

## Generate exercises with AI (optional)

`scripts/generate_exercises.py` turns a topic into a full **language**
lesson with a BYOK model (Anthropic / OpenAI / Gemini) and gates every
draft through the validator before writing it into the `generated/`
staging folder. It is language-focused (target and source differ). For a
**knowledge set** like the ones in this repo (material written in the
same language it teaches, source == target), the generator is not the
right tool; hand-author from
[`templates/knowledge/`](templates/knowledge/) instead.

First set your provider key. It is read from the environment (BYOK) and
never committed:

```bash
export ANTHROPIC_API_KEY="sk-..."   # or OPENAI_API_KEY / GEMINI_API_KEY (Gemini also accepts GOOGLE_API_KEY)
```

**Recommended (via make; reuses the local environment `make validate` set up):**

```bash
make generate ARGS="--topic 'Ordering food in a café' --target-lang fr --source-lang en --level A1 --set-id fr-a1"
```

**Direct (fallback; run it inside the venv from the Quick start):**

```bash
python3 scripts/generate_exercises.py \
  --topic "Ordering food in a café" \
  --target-lang fr --source-lang en --level A1 --set-id fr-a1
```

### Options

| Flag | Default | Meaning |
|------|---------|---------|
| `--topic` | (required) | What the lesson is about. |
| `--target-lang` | (required) | The language the learner studies (BCP-47, e.g. `fr`). |
| `--source-lang` | (required) | The explanation language (BCP-47, e.g. `en`). Must differ from the target. |
| `--level` | `A1` | CEFR level. |
| `--count` | `6` | Exercises to request. The effective minimum is **5** (a smaller value is treated as 5, and the quality gate requires at least 5). |
| `--set-id` | `generated-set` | Staging subfolder under `generated/`. |
| `--provider` | `anthropic` | `anthropic` \| `openai` \| `gemini`. Or set `AL_GEN_PROVIDER`. |
| `--model` | provider default | Override the model (`claude-sonnet-4-5` / `gpt-4o` / `gemini-2.5-flash`). |
| `--retries` | `3` | Extra attempts when a draft fails validation before it is discarded. |
| `--out` | `generated` | Staging directory. |

### What happens, and what you still owe

The script pins the exact lesson-schema JSON in the prompt, parses the
model's reply, and runs it through `validate_content.py`. If validation
fails, the errors go back to the model and it retries (up to `--retries`);
a draft that never validates is discarded, not written. A valid draft
lands in `generated/<set-id>/`, never directly in `sets/`.

Two gates remain after generation, neither of them automatic:

1. **Engine semantic gate** (cloze `___` markers equal the blanks,
   `card_ids` integrity, multiselect disjointness). It runs when the
   pinned `learn-content-engine` is installed, otherwise it is deferred to
   CI. The plain validator does not cover it.
2. **Native-speaker review** for a language you do not speak natively. No
   validator catches an unnatural phrasing or a wrong romanization.
   Machine-generated, then human-verified, is the only trustworthy order.

When a draft is good, move it from `generated/` into your set under
`sets/<source>/<target>-<level>/lessons/`, register it in the set
manifest, and re-run `make validate`.

## How it stays current

The content is validated against the **pinned** engine version in
`schema/engine-version.txt` on every push and pull request (structural +
semantic + drift gates in `.github/workflows/`). A green CI means the
content is valid for every consumer of that engine release. When the
engine is bumped, it reaches this repository the same way it reaches the
rest of the chain: a deliberate pin-bump PR that the drift gate guards.

Licensed MIT (see [LICENSE](LICENSE)); the lesson content carries its own
license via each set manifest's `metadata.license`.
