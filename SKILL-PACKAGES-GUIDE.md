# Image-to-editable PPT skills — two independent packages

This repository now publishes two separately installable modes for converting image-based PPT/PPTX/PDF pages into editable PowerPoint files.

## Choose a package

| Package | Choose it when | Default model profile | Image generation policy |
|---|---|---|---|
| `image-to-editable-ppt-high-fidelity.zip` | Visual similarity is the priority | Strongest available multimodal reasoning model; current local recommendation: GPT-5.6 Sol | Generate material visual assets as needed; no fixed per-page cap |
| `image-to-editable-ppt-low-cost.zip` | Native editability and lower unnecessary generation are the priority | Fast multimodal model; current local recommendation: GPT-5.6 Luna, with Terra for dense pages | Native-first and crop-first; generate complex regions when needed; no artificial fixed cap |

The packages are independent. Install only the mode you want; do not install both for the same run.

## Shared technical requirements

Both packages require:

1. A GPT-capable multimodal model with image input, Chinese reading, structured output, source-pixel coordinates, object classification, and uncertainty marking.
2. The bundled deterministic `editppt`/Open XML PPTX builder.
3. A PowerPoint-capable renderer for visual QA.
4. A concurrency mechanism for independent pages and independent visual assets.

Neither package uses OCR. GPT vision supplies text and visual structure information. Legacy compatibility files may remain in the bundled CLI, but the default workflow passes `--no-text-hints` and does not invoke OCR.

## Why two packages

The underlying reconstruction logic is shared, but the decision priority differs:

- **High fidelity:** material complex illustrations, icons, and visual regions may be generated or regenerated until the page-level comparison is acceptable. Generation count is reported, not capped by a small fixed number.
- **Low cost:** native text, cards, ribbons, rules, connectors, and simple geometry come first; faithful source crops are preferred before generation. Complexity can still justify multiple generation jobs, so the workflow does not promise an artificial maximum.

Both modes preserve the same important boundary: a raster-only region is not falsely described as fully editable.

## Concurrency model

Pages are analyzed and processed concurrently when there is more than one page. Within each page, independent crop, alpha, segmentation, and generation jobs are also concurrent with unique output paths and explicit dependencies. For a one-page deck, asset-level concurrency is the speed mechanism; `--max-concurrent-pages` cannot create more than one page worker.

## Installation

Download one ZIP, extract the contained folder into:

`%USERPROFILE%\\.codex\\skills\\`

The extracted folder name must match the skill name. Validate with the Codex skill-creator `quick_validate.py` before first use.

## Runtime reporting

Each run should report:

- input pages and whether each page has one or many picture objects;
- actual multimodal analysis calls;
- actual ImageGen attempts, successes, retries, and failures;
- elapsed time by page and by asset stage;
- native versus raster-only regions;
- final PPTX path, preview, validation report, and provenance.

Model names above are current recommendations, not hard-coded vendor requirements. An external model can be used when it satisfies the capability contract documented in each package's `references/model-selection.md`.
