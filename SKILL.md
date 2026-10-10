---
name: publish-training-course
description: "Prepare a course from its original recording and courseware: deliver an edited MP4, verified PDF courseware, cover, and a course introduction ready for the training system. Optionally upload and publish to the configured training system only when the user explicitly requests it. Use for course video editing, publication-material preparation, or user-requested course publishing. 触发词：课程发布、发布课程、上架课程、课程剪辑、剪辑培训视频、课件转PDF、课程封面、课程简介、课程素材准备、上传到培训系统。"
metadata:
  version: "1.5.0"
---

# Publish Training Course

## Two-stage workflow

- **Stage 1 (default): prepare.** From the original recording and courseware, deliver and verify four outputs: video, PDF courseware, cover, and course introduction. Stage 1 needs no browser session, academy/category choice, corporate lecturer lookup, or publication decision, and it does not authorize any browser interaction.
- **Stage 2 (optional): upload or publish.** Only when the user explicitly asks the assistant to upload or publish in the training system. The user may publish the delivered materials themselves; never make publication a mandatory question or block Stage 1 on it. If publication was requested in this task, continue into Stage 2 after Stage 1; a later request reuses the delivered files unless a revision is requested or validation finds a problem. Follow [Stage 2 publishing](references/publishing.md).

Authorization rules shared by both stages:

- An upload-only request authorizes upload work, not `提交并发布`. Click `提交并发布` only after explicit authorization for the same course, version, and publication scope in this task; reconfirm only if that scope materially changes.
- Preserve original courseware, recordings, cover candidates, and existing outputs; always create distinct derived filenames.
- Do not install codecs/tools or bypass enterprise access controls without authorization. Never expose credentials, session tokens, private chat content, or unrelated personal information.

## Video editing choices

**Default: no edit.** Preserve the complete original timeline, including setup, waiting, silence, and post-session footage. Do not trim, split, remove segments, or crop borders unless the user explicitly requests that edit. “不需要切割” means keep the full timeline. A user-provided time range is authoritative.

Only when an edit is requested, confirm these two choices unless already stated. A pending choice blocks only the dependent video work; continue courseware reading, PDF conversion, and title/introduction drafts meanwhile.

1. **Trim mode:** `AI判断剪辑边界` or `用户提供时间轴`. In the timeline mode, ask for the exact start and end of each course or segment if not supplied. In the AI mode, remove only unambiguous invalid footage; when a segment may contain teaching content, preserve it or ask.
2. **Border crop:** `需要裁掉多余边框` or `不裁边框`. Determine the crop from representative frames of this recording; never reuse another video's rectangle. If no single safe crop preserves teaching content throughout, keep the content and report the conflict.

## Course names and deliverables

- Read the complete courseware before naming. Use its main teaching title without unrelated dates, file-version suffixes, or labels such as `培训回放`; keep a subtitle only when it distinguishes the topic.
- Do not put the lecturer in `课程名称` or the video filename. Use lecturer information only when the source materials support it; corporate lecturer identity matters only for Stage 2.
- When one event contains several courses, name each course by its own courseware topic, not the broad event name.
- Use exactly these names (the full-width brackets are literal; do not use the former `-视频`, `-资料`, or `-课件` suffixes):
  - Video: `【视频】<课程名称>.mp4`
  - Courseware: `【资料】<课程名称>.pdf`
  - Cover: `<课程名称>-封面.png` (or `.jpg` when appropriate)
  - Introduction: `<课程名称>-课程简介.txt`, containing only the paste-ready introduction
- Write outputs into the original course folder beside the sources by default. If it is not writable, say so before using another location.
- Do not create a separate publication note. Trim ranges, technical checks, and inventories are working information; report relevant verification briefly at delivery.

## Prepare and verify (Stage 1)

1. Inspect the whole original course directory without modifying it: source courseware, original recording, existing processed video, cover candidates, and notes.
2. Read the complete courseware with the appropriate presentation/document skill; do not infer facts from a filename or first slide. Convert it to `【资料】<课程名称>.pdf` by the [verified PDF route](references/pdf-conversion.md) (render slides to images, inspect, assemble). Never use WPS, including as a fallback. Verify page count, rendered pages, and readable layout.
3. Inspect video metadata, codecs, audio, and representative frames at the beginning, middle, and end.
4. Produce `【视频】<课程名称>.mp4` as upload-compatible H.264/AAC. Without a requested edit, avoid unnecessary re-encoding: use a full-duration copy, or transcode without changing content when the source is incompatible. For requested edits follow [video editing](references/video-editing.md). Verify duration, dimensions, codecs, audio, start/middle/end frames, and a full decode.
5. Cover: prefer an approved cover or a suitable 16:9 export of the first slide; otherwise use the image-generation skill. Check title text, safe margins, contrast, and thumbnail legibility.
6. Write a concise one-paragraph introduction grounded in the full courseware and available recording evidence. It is the text for the system's `课程简介` / `简介` field: no paths, trim ranges, codecs, upload instructions, or unsupported claims. Save it to `<课程名称>-课程简介.txt` and show it in the reply; drafting it needs no approval.
7. Deliver links to the MP4, PDF, cover, and introduction with a brief verification result. Stage 1 is complete when all four are delivered and validated.

## Stage 1 failure handling

- If a source recording cannot be obtained through an authorized path, report only that item and continue the rest.
- In `用户提供时间轴` mode use the supplied ranges exactly unless the user revises them; in `AI判断剪辑边界` mode preserve any boundary that remains unproven.
- If PDF rendering fails or stays visually defective, report the concrete blocker, continue the other outputs, and never label an unchecked PDF successful.
