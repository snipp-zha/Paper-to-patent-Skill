# `nature-paper-to-patent` skill

An evidence-grounded workflow for converting scientific papers, theses,
technical reports, source code, figures, and inventor notes into structured
Chinese invention patent drafts.

The skill is designed for graduate students, university researchers, and
technical teams. It produces formal patent documents in Chinese while keeping
agent-facing analysis and routing instructions portable across AI agents.

## What it does

- maps paper text, equations, figures, code, and supplementary evidence to
  stable source IDs
- extracts the technical problem, cooperating technical means, implementation
  chain, and specific technical output
- builds an evidence ledger before drafting claims
- maps every material claim feature to source evidence
- supports full drafts, claim-only drafting, disclosure analysis, and
  paper-patent comparison
- handles selectable PDFs, scanned PDFs, pasted text, and mixed project folders
- routes algorithm/software, apparatus/system, process/material, and mixed
  inventions to different drafting rules
- preserves source-supported core formulas and renders them as editable Office
  Math in DOCX
- generates claim-aligned black-and-white flowcharts as SVG and PNG
- reuses the principal claim flowchart as both the abstract figure and a
  specification figure
- produces separate Chinese DOCX files for claims, specification, abstract,
  abstract figure, and a combined review draft
- validates claim structure, evidence traceability, equation coverage, figure
  alignment, terminology consistency, and quality thresholds

## File structure

```text
nature-paper-to-patent/
├── README.md
├── SKILL.md
├── manifest.yaml
├── requirements.txt
├── static/
│   ├── core/
│   │   ├── principles.md
│   │   ├── workflow.md
│   │   └── output-contract.md
│   └── fragments/
│       ├── source/
│       ├── task/
│       └── invention/
├── references/
│   ├── cn-patent-drafting-guide.md
│   ├── corpus-derived-patterns.md
│   ├── corpus-pair-audit.md
│   ├── draft-schema.md
│   └── patent-figure-guide.md
├── scripts/
│   ├── audit_claims.py
│   ├── build_patent_package.py
│   ├── extract_pdf_text.py
│   ├── init_patent_project.py
│   ├── math_to_omml.py
│   ├── render_flowchart_svg.py
│   ├── render_patent_docx.py
│   └── validate_patent_draft.py
├── evals/
│   └── evals.json
└── tests/
    └── test_validation.py
```

## Routing model

The short `SKILL.md` acts as a router. `manifest.yaml` selects only the
fragments needed for the current request:

- `source_format`: `pdf-text`, `scanned-pdf`, `pasted-text`, or
  `mixed-project`
- `task_mode`: `full-draft`, `claim-set`, `disclosure-analysis`, or
  `paper-patent-audit`
- `invention_type`: `algorithm-software`, `apparatus-system`,
  `process-material`, or `mixed`

The always-loaded core defines evidence discipline, the staged drafting
workflow, and the output contract.

## Default workflow

1. Record inputs, publication status, inventorship questions, and ownership
   questions.
2. Build a full-document source map.
3. Build terminology, formula, figure, and input-operation-output inventories.
4. Build the evidence ledger.
5. Form the invention concept and claim strategy.
6. Draft and audit claims.
7. Align the specification, equations, figures, embodiments, and abstract.
8. Validate and build the complete application package.

Each stage has an explicit gate. Unsupported features are excluded from formal
claims, and unresolved facts are retained as inventor questions instead of
being invented.

## Installation

Install the complete directory, not only `SKILL.md`, because the router depends
on `manifest.yaml`, `static/`, `references/`, and `scripts/`.

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

Run automated checks:

```bash
python -m unittest discover -s tests -v
```

## Example request

```text
Read and follow the nature-paper-to-patent skill.
Analyse paper/paper.pdf and generate a Chinese invention patent draft.
Create separate Chinese claims, specification, abstract, and abstract-figure
DOCX files. Preserve the source-supported core equations as editable Office
Math, generate a claim-aligned main flowchart and methodology figures, and map
every material claim feature to its source evidence.
```

## Default deliverables

```text
outputs/
├── patent-权利要求书.docx
├── patent-说明书.docx
├── patent-说明书摘要.docx
├── patent-摘要附图.docx
├── patent-完整审阅稿.docx
├── patent-结构化草稿.json
├── patent-权利要求检查.txt
├── patent-草稿验证报告.txt
└── patent-figures/
    ├── figure-1.svg
    └── figure-1.png
```

## Status and limits

Status: **Beta**.

The deterministic validation scripts and synthetic tests cover claim mapping,
core-equation requirements, figure generation, editable Office Math, and DOCX
packaging. The output remains a drafting aid. It is not a patentability,
novelty, inventorship, ownership, infringement, or filing-readiness opinion,
and it requires inventor confirmation and qualified Chinese patent
professional review.

Contributed by
[`snipp-zha`](https://github.com/snipp-zha).

The standalone development repository is
[`snipp-zha/Paper-to-patent-Skill`](https://github.com/snipp-zha/Paper-to-patent-Skill).
