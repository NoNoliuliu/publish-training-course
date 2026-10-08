# Courseware PDF conversion without WPS

The user explicitly excludes WPS from this workflow. Do not launch it or use its UI, automation, or export functions, including as a fallback.

## Verified route

On 2026-08-20, two demand-analysis course decks were successfully delivered as 12-page and 11-page PDFs using rendered slide images plus ReportLab. Direct LibreOffice PDF export had missing text; it was replaced by this route. Final PDFs were checked with pdfinfo, rendered again with pdftoppm, and visually inspected. This is historical success on those decks, not a guarantee for every new deck.

1. Load the current Presentations and PDF skills and resolve the bundled runtime with `load_workspace_dependencies`. Locate the current Presentations `container_tools/render_slides.py`; do not hardcode a versioned plugin-cache path from the historical run.
2. Run the current renderer on the original PPTX into a fresh temporary directory, using the bundled Python and the runtime paths required by its helper. Inspect `--help` and renderer implementation when the interface has changed. The currently inspected helper uses `render_presentation.mjs` and artifact-tool for presentation rendering, not WPS. Historical invocation shape:

   `"$course_python" "$course_render_slides" "$course_source_pptx" --output_dir "$course_slide_dir"`

3. Confirm the rendered slide count matches the original presentation. Inspect every page for Chinese glyph loss, clipping, wrong layout, and unreadable text. Use a montage for overview and full-size views for dense or suspicious pages. Do not turn already defective images into the final PDF.
4. Sort `slide-*.png` by numeric slide number, not lexicographic filename order. Use Python Pillow and `reportlab.pdfgen.canvas` / `reportlab.lib.utils.ImageReader` to draw one complete image per PDF page. Derive page size and aspect ratio from this source presentation; the historical 960 × 540 pt setting was specific to those 16:9 decks. Preserve the full slide without stretching or cropping. Write a distinct derived `【资料】<课程名称>.pdf`; preserve the source and previous outputs.
5. Check the final PDF page count and page dimensions with `pdfinfo` or a PDF library, render it again with `pdftoppm`, and visually verify the resulting pages. Reuse the verified first-slide image for a cover when suitable.

This creates an image-based PDF: visible content is preserved only to the fidelity of the checked rasterization; text is not inherently selectable or searchable. Briefly disclose this at delivery. If searchable text is specifically required, resolve that requirement separately without invoking WPS or silently promising a text layer.

If rendering fails or remains visually defective, report the concrete blocker and continue the independent video, cover, and introduction work where possible. Do not switch to WPS, install tools without authorization, or label an unchecked PDF successful.
