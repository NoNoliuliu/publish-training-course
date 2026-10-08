---
name: publish-training-course
description: "Prepare a course from its original recording and courseware: deliver an edited MP4, verified PDF courseware, cover, and a course introduction ready for the training system. Optionally upload and publish to the configured training system only when the user explicitly requests it. Use for course video editing, publication-material preparation, or user-requested course publishing."
metadata:
  version: "1.4.1"
---

# Publish Training Course

## Two-stage workflow

Stage 1 is the default: given the original recording and original courseware, prepare and verify four deliverables: a verified video, PDF courseware, cover, and course introduction. Deliver all four without requiring a browser session, academy/category selection, corporate lecturer lookup, or a decision about who will publish. Preparing these materials does not authorize browser interaction or publication.

## Default handling

- **Output location:** Write derived course outputs into the original course folder beside the source recording and courseware by default. Preserve originals and existing outputs, and use distinct filenames. If the original folder is not writable, report that before writing to an alternative location.
- **Video:** Preserve the complete original timeline by default. Do not trim, split, remove segments, crop borders, or apply other content edits unless the user explicitly requests an edit. A user-provided time range is authoritative; “不需要切割” means keep the full source timeline.
- **Publication:** Preparation is the default. Do not upload or publish a course unless the user explicitly requests upload or publication in the current task.

Stage 2 is optional: operate the training system only when the user explicitly asks the assistant to upload or publish the course. The user may publish the delivered materials themselves. Do not make the publication decision a mandatory question or block delivery on it. If publication was already requested in this task, continue into Stage 2 after preparing the materials and satisfying its content-approval requirements; otherwise finish after Stage 1. A later publication request should use the delivered files and introduction without regenerating them unless a revision is requested or validation finds a problem.

For Stage 2, operate the existing signed-in Chrome session. Use the Chrome control skill for DOM interactions and use Computer Use only when the user needs the selected tab brought to the foreground. Target labels, placeholders, rows, dialogs, and buttons; do not rely on screen coordinates or stale accessibility indexes.

## Confirm the editing choices first

Only when the user requests a video edit, confirm these two decisions unless the user has already stated them. Pending choices block only the dependent video work; continue independent courseware reading, PDF conversion, material verification, and title/introduction drafts. If no video edit is requested, skip these choices and preserve the complete recording:

1. **Trim mode:** either `Codex判断剪辑边界` or `用户提供时间轴`. If the user chooses the timeline mode but has not supplied ranges, ask for the exact start and end time of each course or segment.
2. **Border crop:** either `需要裁掉多余边框` or `不裁边框`. If cropping is requested, inspect representative frames and crop to the stable teaching-content area; do not reuse fixed pixel coordinates from another recording.

In the default no-cut mode, preserve all footage, including setup, waiting, silence, and post-session material. If the user requests `Codex判断剪辑边界`, remove only unambiguous invalid footage; if a segment may contain teaching content or its status is uncertain, preserve it or ask the user instead. User-provided exact time ranges remain authoritative.

## Publication defaults and shared boundaries

System-field defaults and publication authorization below apply only to Stage 2. Source preservation applies to both stages.

- Treat the user's academy/category choice as authoritative. The material-directory choice is the same academy path (for example, `目标学院`), not a new department choice.
- Keep the current authorized `所属部门` unless the user explicitly changes it.
- Set `学习时长` to **1 minute for every material by default**, including video and courseware. Use another value only when the user explicitly supplies one. Report the verified media runtime briefly at delivery, but do not substitute it for this row default.
- Set `是否可以下载` to `否` unless the user explicitly requests downloads.
- Preserve visible defaults for `公开范围`, `是否必学`, recommendation, topic-course, exercises, and examinations unless the user gives another value.
- Do not click `提交并发布` until the user has explicitly authorized the live publication, unless explicit authorization for the same course, version, and publication scope is already present in this task. Reconfirm only if that scope materially changes. A preparation-only or review-only request does not authorize publication.
- Preserve original courseware, recordings, and cover candidates. Create distinct derived filenames.

## Course names and deliverables

- Read the complete courseware before naming the course. Use its main teaching title, removing unrelated dates, file version suffixes, and labels such as `培训回放`. Keep a subtitle only when it materially distinguishes the topic.
- Do not add the lecturer to the system `课程名称`. Use lecturer information only when supported by the source materials. Corporate lecturer identity is required only for Stage 2; an unresolved identity does not block Stage 1 delivery. The video filename does not include the lecturer.
- When one training event contains multiple courses, use each courseware topic as a separate course name. Do not use the broad event name in place of the individual course titles.
- Use the following exact naming convention for the generated files and Stage 2 material rows; the full-width brackets are literal and must be retained. Do not substitute the former `-视频`, `-资料`, or `-课件` suffixes.
- For each course, deliver these four outputs when both original video and courseware are supplied:
  - Video: `【视频】<课程名称>.mp4`
  - Courseware: `【资料】<课程名称>.pdf`
  - Cover: `<课程名称>-封面.png` (or `.jpg` when appropriate)
  - Course introduction: `<课程名称>-课程简介.txt`, containing only the introduction text ready to paste into the system
- Do not create a separate publication note by default. Trim ranges, technical checks, and source inventories are working information, not mandatory user-facing documents. Briefly report relevant verification results at delivery.
- Preserve the source video and source PPT/PPTX. Do not overwrite existing processed outputs; use a distinct filename when regenerating a revision.

## Prepare and verify the package

1. Inspect the complete original course directory without modifying originals. Identify source courseware, original recording, existing processed video, cover candidates, and publication notes. Set the default output directory to this same original course folder.
2. Read the complete PPTX or other courseware with the appropriate presentation/document skill. Do not infer course facts from a filename or first slide alone. Convert each final courseware file to `【资料】<课程名称>.pdf` using the [verified PDF conversion route](references/pdf-conversion.md): render the original slides to images, inspect them, then assemble the images into PDF. Do not invoke WPS for conversion, including as a fallback. Verify page count, rendered pages, and readable layout.
3. Inspect video metadata, codecs, audio, and representative frames at the beginning, middle, and end. Preserve the complete source timeline by default. Apply a trim, split, removal, or border-crop choice only when the user explicitly requests the corresponding edit and the choice is confirmed.
4. Produce `【视频】<课程名称>.mp4` as an upload-compatible H.264/AAC MP4 while preserving the complete source timeline by default. If no edit was requested and the source is already compatible, avoid unnecessary re-encoding; when a distinct derived output is needed, use a full-duration copy or transcode without changing content. Verify duration, dimensions, codecs, audio, start/middle/end sample frames, and a full decode check.
5. Prefer an approved cover or a visually suitable 16:9 export of the first courseware slide. Otherwise use the image-generation skill. Visually verify title text, safe margins, contrast, and thumbnail legibility.
6. Write a concise, one-paragraph course introduction grounded in the complete courseware and available recording evidence. It is the actual text for the system's `课程简介` / `简介` field, not a processing report. Do not include source paths, trim ranges, codecs, upload instructions, or unsupported claims. Save it as `<课程名称>-课程简介.txt` with only the paste-ready text; also show the introduction in the delivery response. Drafting and delivering the title/introduction do not require content approval.
7. Deliver links to the edited MP4, verified PDF, cover, and introduction file, plus a brief verification result. Stage 1 is complete when all four are delivered and validated; do not require publication-specific information to finish it.

## Preferred video editing path

When no video edit is requested, do not invoke a trim command: preserve the complete source timeline and content. Use the following FFmpeg path only for an explicitly requested trim, segment extraction, or crop.

For ordinary timeline trimming, prefer the proven FFmpeg path in [video-editing.md](references/video-editing.md). On this macOS environment `ffmpeg` and `ffprobe` are installed natively (arm64) and available on `PATH` at `~/.local/bin/`. If a restricted shell does not resolve `ffmpeg`, probe `~/.local/bin/ffmpeg` directly, then the legacy `~/.workbuddy/bin/ffmpeg` (x86_64, Rosetta), before concluding that FFmpeg is unavailable. Do not try AVFoundation first when any of these binaries is usable.

- For an explicitly requested edit, default to accurate H.264/AAC re-encoding with input-side `-ss`, `-t`, `libx264 -preset veryfast -crf 21`, `yuv420p`, AAC, and `+faststart`. This is the preferred balance of speed, exact user-supplied boundaries, and training-platform compatibility.
- Preserve the source frame cadence and audio layout unless the source is incompatible with the training system or the user requests normalization. Do not force 15/30 fps, mono/stereo, or a sample rate merely because an earlier recording used it.
- Border cropping or any other video filter requires re-encoding. Determine crop coordinates from representative frames of the current recording; never reuse another video's crop rectangle.
- Treat `-c copy` as an optional validated fast path, not the default. Use it only when keyframe/edit-list behavior, exact visible boundaries, duration, audio alignment, and target-player compatibility have been checked for that source. Fall back to re-encoding when boundary portability matters.
- Use a distinct output path in the original course folder and a no-overwrite guard. After editing, verify metadata, exact duration, start/middle/end frames, audio presence, and full decoding before delivery.

## Enter optional Stage 2 only on request

- Require an explicit request to upload or publish in the training system. If none is present, finish with the four deliverables and leave publication to the user.
- Before filling the live form, obtain approval for the exact course title and introduction unless that same content is already approved in this task. This approval does not block Stage 1 delivery.
- Verify the delivered cover, MP4, PDF, and introduction are available. Obtain only missing publication-specific information: the user-selected academy/category and the verified lecturer identity. Use existing answers without asking again.
- An upload-only request authorizes upload work, not `提交并发布`. Preserve the existing explicit-publication authorization rule.

Do not install codecs/tools or bypass enterprise access controls without authorization. Never expose credentials, session tokens, private chat content, or unrelated personal information.

## Attach to the live course page safely

1. Prefer the existing signed-in Chrome tab. List open tabs and claim the exact tab whose title/URL match the training system. Do not create a second `新增课程` tab when an unfinished one exists.
2. If multiple same-title tabs exist, inspect their DOM values. Resume the tab containing the prepared title/intro/materials; do not switch to a blank duplicate. If the user cannot see it, bring that selected tab/group to the foreground after obtaining a fresh window state.
3. Confirm the page is `新增课程`. If a form is partially filled, preserve and resume it. If the course already exists in the course list, stop and ask whether to edit it or create a distinct course.
4. After any browser-connection reset, reacquire a tab from a fresh `openTabs()` result, reread the DOM, and revalidate every field. Never assume an old tab handle or unsaved values still exist. Mark an unfinished page for handoff and the verified result page as the deliverable.

## Fill the basic section

1. Keep the current `所属部门` unless explicitly changed.
2. Fill `课程名称` with the approved title.
3. Select the exact user-approved `所属学院` and `课程分类`, then read both values back. If the user has already specified both values, do not pause for a second handoff and do not infer a different path.
4. Upload the cover by clicking the visible `课程图片` plus control, waiting for the file chooser, and setting the verified cover path. Verify a preview/image source appears. If the chooser times out, inspect a fresh DOM/screenshot and retarget the actual plus control; do not repeatedly click a stale locator.
5. Set `课程标签` only when supported by the materials or user instruction.
6. Fill the system's `课程简介` / `简介` field with the approved text from `<课程名称>-课程简介.txt`. Leave recommendation and topic-course settings unchanged unless explicitly supplied.

## Add content: upload local files

Create one row for the edited video and one row for the verified PDF courseware when courseware should be offered.

For each row:

- Set the row name to `【视频】<课程名称>` or `【资料】<课程名称>`, matching the corresponding filename.
- Select the exact lecturer identity from the corporate directory; prefer the displayed employee identifier when present (for example, `讲师姓名(工号)`). If multiple people remain, ask the user to choose.
- Set `学习时长` to `1` minute by default.
- Set `是否可以下载` to `否` by default.

### Upload route: `+上传文件`

Upload the prepared local MP4 and PDF directly. Do not search or select files from the material library in this workflow. After creating a row with `+新增行`, upload its corresponding file.

1. Click the row's `+上传文件` and wait for `素材上传`.
2. In the dialog's `目录`, select the exact academy directory. The directory menu can remain open after selecting its radio. Close it by clicking the same directory textbox again; **do not press Escape**, because Escape can close the entire upload dialog.
3. With the menu closed, click the dialog's hidden `input[type="file"]` through a file-chooser event and set the exact publication file. Do not click a visible file button while the directory menu overlay is open.
4. Wait for `分片完成，可以上传`, click `开始上传`, and poll until the row says `已上传`. Fill the material title, click `保存`, and verify `新增素材成功` plus the filename on the course row.
5. If Chrome reports that the file is not allowed, ask the user to enable the ChatGPT/Chrome extension's access to file URLs and restart Chrome, then resume the same form. Do not discard the form or re-upload blindly.

After each upload, take a fresh DOM snapshot and checkpoint the row name, lecturer, duration, download setting, and filename before moving to the next row.

## Review and publish

Before the final action, verify:

- Course name, current department, user-selected academy/category, introduction, and cover preview.
- Every material row: row name, exact lecturer, `1`-minute duration (unless explicitly overridden), `否` download setting, and uploaded filename.
- Visible access, required-learning, recommendation, and topic-course defaults.

After explicit user authorization (including valid authorization already given for this task), click `提交并发布` once. Wait for a success indication such as `新增课程成功`; do not repeatedly resubmit after an error. Verify the course list by searching the exact title and confirm the row shows `已发布`, recording the course ID when available. Keep the verified result tab visible and mark it as the deliverable.

## Failure handling

- If a source recording cannot be obtained through an authorized path, report only that missing item and continue safe preparation.
- If the user has not requested video editing, preserve the complete source timeline and do not remove footage merely because it appears non-teaching or idle.
- In `Codex判断剪辑边界` mode, if a boundary remains unproven after inspection, preserve the teaching content and remove only unambiguous invalid footage. In `用户提供时间轴` mode, use the supplied ranges exactly unless the user revises them.
- If border cropping is requested but a single safe crop cannot preserve teaching content throughout the video, preserve the content and report the conflict instead of applying an unsafe crop.
- If authentication expires, ask the user to sign in in the selected Chrome session and confirm before resuming.
- If an upload stalls, preserve the form and record the filename and visible status/error. Inspect the current upload dialog and course row to determine whether the upload is still running, completed, or explicitly failed. Continue waiting for active uploads; retry the local file only after a confirmed failure. If the outcome cannot be established, report the uncertainty and ask before uploading another copy.
- After a browser reset, reacquire the user tab and inspect the current form. Preserve any existing file associations and rebuild only missing fields. If the form or an uploaded-file association has disappeared and the previous upload outcome cannot be established, report the uncertainty and ask before uploading again; do not switch to the material library.
- If the academy/category is blank after handoff, return control to the user; never guess.
- If a final publish succeeds, verify the list record and status rather than relying only on a transient toast.
