---
name: paper-to-cn-patent
description: Convert scientific papers, theses, technical reports, or research manuscripts into evidence-grounded Chinese invention patent application packages. Use when an AI agent must analyze a paper, identify patentable technical contributions, preserve core formulas with symbol definitions in the specification, design and audit claim sets, generate claim-aligned black-and-white flowcharts as SVG and PNG, embed figures in the specification, compare a paper with a patent, or deliver separate DOCX files for the specification, claims, and abstract plus a combined review draft.
---

# Paper to Chinese Patent

## Purpose

Turn research disclosure into a structured Chinese invention patent draft while preserving technical support and marking every unresolved fact. Treat the result as a drafting aid, not a legal opinion or a substitute for a qualified patent professional.

Use English for agent-facing reasoning, status updates, schemas, and
instructions unless the user requests another language. Produce all formal
Chinese patent deliverables in Chinese, including claims, specification,
abstract, figure descriptions, figure labels, and inventor confirmation
questions.

## Required Inputs

Obtain the paper or technical disclosure. Also use supplementary material, source code, diagrams, datasets, experiment settings, prior patent drafts, and inventor notes when available.

Determine or state the following assumptions:

- Target: Chinese invention patent unless the user specifies another jurisdiction.
- Deliverable: invention disclosure, claim set, full application draft, or support audit.
- Filing status: whether the paper has been published and its publication date.
- Inventorship and ownership: do not infer these from author order.

If essential facts are missing, continue with explicit `[TO CONFIRM]` markers instead of inventing details.

## Workflow

### 1. Extract the Source

For PDFs, run `scripts/extract_pdf_text.py`. Install `pypdf` with `python -m pip install pypdf` if the import is unavailable. If extraction yields little text, report that OCR is required.

Identify:

- title, abstract, technical field, and stated problem;
- inputs, outputs, components, process steps, formulas, and parameter ranges;
- each claimed contribution and its stated technical effect;
- experimental settings, baselines, results, figures, and implementation details;
- limitations, alternatives, and future work.

Preserve page markers or another source locator.

Create a formula inventory whenever the source contains equations. For each formula record:

- source number and page;
- original expression and a valid LaTeX source expression;
- symbols and definitions;
- role in the technical process;
- destination in the claims or specification.

Do not reduce an algorithm paper to prose when its core operations are defined by formulas.
Set `source_analysis.contains_core_formulas` in the structured draft. The package builder must fail when this value is true and the specification has no equations.

Create a figure inventory for architecture, module, signal-path, data-flow, and method figures. Set `source_analysis.contains_methodology_figures` when one or more figures explain a core technical operation rather than experimental results.

### 2. Build an Evidence Ledger

Before drafting claims, create a compact ledger with these columns:

| ID | Proposed feature | Source location | Technical role | Effect | Support status |
|---|---|---|---|---|---|

Use only these support states:

- `explicit`: directly disclosed;
- `inherent`: necessarily follows from the disclosed operation;
- `needs-confirmation`: plausible but not adequately disclosed;
- `unsupported`: exclude from claims and embodiments.

Do not convert experimental speculation, future work, or a desired effect into a disclosed technical feature.

When a paired patent is supplied, create a separate mapping table with:

| Feature | Paper support | Patent location | Relationship | Risk |
|---|---|---|---|---|

Use `same`, `narrower-in-patent`, `broader-in-patent`, `renamed`, `patent-only`, or `paper-only` for the relationship. Never assume two files are strict counterparts merely because their numbering matches. Read `references/corpus-pair-audit.md` for examples of this distinction.

### 3. Form the Invention Concept

Express the invention as:

`technical problem -> cooperating technical means -> observable technical effect`

Separate the essential chain from optional implementations. Prefer functional wording only when the paper discloses enough structure or steps to support that function.

For algorithms, anchor the claim in concrete data acquisition, signal or image processing, model operations, and a specific domain result or control action. Name the result precisely, such as a defect detection result, fault detection result, target pose, health-state estimate, or control instruction. Avoid presenting a mathematical rule in isolation.

Read `references/cn-patent-drafting-guide.md` before drafting a full application or claims.

### 4. Design the Claim Architecture

Draft the broadest source-supported independent claim first.

For a computer-implemented invention, usually consider:

1. a method independent claim;
2. dependent claims for data preparation, core modules, training, inference, formulas, parameters, and post-processing;
3. an electronic device claim;
4. a computer-readable storage medium claim.

Include system, apparatus, product, or use claims only when technically appropriate and supported. Do not add claim categories mechanically.

Keep the independent claim focused on the minimum cooperating features that produce the technical effect. Move implementation detail, preferred values, formulas, layer counts, and dataset-specific choices into dependent claims unless they are essential.

### 5. Draft in This Order

Draft:

1. independent and dependent claims;
2. invention title;
3. technical field;
4. background;
5. technical problem, solution, and beneficial effects;
6. figure plan and figure descriptions;
7. black-and-white method flowchart;
8. detailed embodiments;
9. abstract.

Use consistent Chinese terminology throughout. Introduce every symbol before using it. Ensure every claim feature is described in the specification and every figure is referenced.

For an algorithm, signal-processing, control, estimation, or model-training invention:

- include the source-supported core formulas in the specification;
- number formulas consecutively;
- explain every symbol immediately after the formula;
- explain the technical operation performed by the formula;
- include formulas for essential sampling, transformation, loss, weighting, fusion, estimation, or decision operations;
- do not include evaluation metrics merely to inflate formula count unless they support implementation or verification.
- render every formal equation as native editable Office Math in DOCX, not as a plain-text LaTeX or Unicode approximation;
- retain LaTeX only as structured source input for reproducible conversion;
- verify that the generated DOCX contains `m:oMath` elements.

For a method flowchart:

- derive the main nodes from the ordered steps of the method independent claim;
- keep claim step identifiers such as `S1`, `S2`, and `S3` identical in the claim, figure, figure description, and embodiment;
- use short operational labels, not effect-only language;
- make the final node output or obtain the specific domain result, such as a defect detection result or target detection result;
- never use vague final-node wording equivalent to "technical result", "processing result", or "final result";
- show branches only when the source discloses a real conditional or parallel path;
- avoid placing formulas, performance values, or long implementation details inside nodes.

Use the main method flowchart as the abstract figure by default. Reuse the exact same figure as a specification figure when it accurately represents the invention's principal technical flow. Do not redraw a second version with different wording or step order.

In addition to the main flowchart, include methodology figures for the core intermediate operations when they materially aid enablement. Prefer a clear figure from the paper when its content and labels are suitable for patent use; otherwise redraw a black-and-white patent-style methodology diagram. A methodology figure may show architecture, parallel branches, feature transformation, loss relationships, or module inputs and outputs, and need not map one-to-one to claim step identifiers. Do not use result charts, accuracy tables, or ablation plots as substitutes for methodology figures.

### 6. Run the Support Audit

Check:

- every claimed feature has an evidence-ledger entry;
- claim dependencies are valid and narrow the referenced claim;
- no module name hides undisclosed operations;
- technical effects follow from the claimed features;
- equations, symbols, units, and ranges are defined consistently;
- examples do not silently limit the general invention;
- device and medium claims correctly refer back to the method;
- the abstract contains no unsupported promise;
- authorship, inventorship, ownership, filing dates, and legal conclusions are not guessed.

Use `[TO CONFIRM: specific question]` for unresolved points. List all such points at the end.

Save the claims as UTF-8 text and run:

```bash
python scripts/audit_claims.py claims.txt
```

Treat script findings as drafting checks, not legal conclusions. Resolve all `ERROR` findings. Review each `WARNING` against the source and specification.

### 7. Produce the Deliverable

For a complete application, populate the JSON structure in `references/draft-schema.md`, including `specification.equations` when the source contains formulas. Then run the package builder:

```bash
py -3 -m pip install python-docx pillow latex2mathml
py -3 scripts/build_patent_package.py draft.json --output-dir deliverables --prefix patent
```

The complete package must contain:

- a standalone claims DOCX;
- a standalone specification DOCX;
- a standalone abstract DOCX;
- a standalone abstract-figure DOCX;
- a combined review DOCX;
- an editable SVG flowchart;
- a directly viewable PNG flowchart embedded in the specification DOCX;
- the structured JSON source;
- a claims audit report.

Do not treat an SVG path alone as sufficient delivery of a figure. Generate both SVG and PNG. Embed the same designated abstract figure in the standalone specification, standalone abstract, abstract-figure document, and combined review document. Resolve every figure validation error before delivery.

Keep evidence-ledger content and unresolved questions in the review appendix. Do not place internal support labels inside the formal claims unless the user explicitly requests an annotated draft.

Before delivery, score the draft with the rubric in `references/cn-patent-drafting-guide.md`. Do not label a full draft ready for review unless:

- evidence support is at least 4/5;
- claim architecture is at least 4/5;
- terminology and dependency consistency is at least 4/5;
- enablement detail is at least 3/5;
- no unresolved `ERROR` remains from `audit_claims.py`.

## Output Format

For a full draft, return:

1. source and assumption summary;
2. invention concept;
3. evidence ledger;
4. claims;
5. specification;
6. abstract;
7. support and consistency audit;
8. inventor questions.

When producing files, prefer both:

- a structured UTF-8 JSON source for repeatable editing;
- separate formatted DOCX files for claims, specification, and abstract;
- a separate abstract-figure DOCX using the same main flowchart;
- a combined DOCX for review;
- one editable SVG and one viewable PNG per patent figure.

When the user requests only a claim set or analysis, output only the relevant sections but retain the evidence and uncertainty discipline.

## Corpus Guidance

Read `references/corpus-derived-patterns.md` when choosing how to transform academic contributions into claims. Read `references/corpus-pair-audit.md` before relying on the five paired examples as ground truth. The patterns are drafting heuristics, not legal rules.
