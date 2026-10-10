# Stage 2: upload or publish in the training system

Read this only when the user explicitly asks the assistant to upload or publish. The authorization rules in SKILL.md still apply: upload-only does not authorize `提交并发布`.

## Before touching the live form

- Verify the delivered cover, MP4, PDF, and introduction exist.
- Obtain approval for the exact course title and introduction unless already approved in this task. This never blocks Stage 1 delivery.
- Ask only for missing publication-specific information: the user-selected academy/category and the verified lecturer identity. Reuse answers already given.
- Use the user's existing signed-in browser session through whatever browser-control tools this assistant has. Target labels, placeholders, rows, dialogs, and buttons; do not rely on screen coordinates or stale element references. Bring the tab to the foreground only when the user needs to see it.

## System-field defaults

- The user's academy/category choice is authoritative. The material-directory choice is the same academy path (for example, `目标学院`), not a new department choice.
- Keep the current authorized `所属部门` unless the user explicitly changes it.
- `学习时长`: **1 minute for every material** (video and courseware) unless the user supplies another value. Report the verified media runtime at delivery, but do not substitute it.
- `是否可以下载`: `否` unless the user explicitly requests downloads.
- Keep visible defaults for `公开范围`, `是否必学`, recommendation, topic-course, exercises, and examinations unless told otherwise.

## Attach to the live course page

1. List open tabs and claim the exact training-system tab by title/URL. Do not open a second `新增课程` tab while an unfinished one exists.
2. With several same-title tabs, inspect their field values and resume the one holding the prepared title/introduction/materials, not a blank duplicate.
3. Confirm the page is `新增课程`. Preserve and resume a partially filled form. If the course already exists in the course list, stop and ask whether to edit it or create a distinct course.
4. After any browser-connection reset, reacquire the tab from a fresh tab list, reread the page, and revalidate every field. Never assume an old tab handle or unsaved values survive.

## Basic section

1. Keep `所属部门`; fill `课程名称` with the approved title.
2. Select the exact `所属学院` and `课程分类` and read both back. If the user already specified both, do not pause for another handoff or infer a different path.
3. Upload the cover via the visible `课程图片` plus control and the file chooser; confirm a preview appears. If the chooser times out, retarget the actual control from a fresh view instead of re-clicking a stale locator.
4. Set `课程标签` only when supported by the materials or the user.
5. Fill `课程简介` / `简介` with the approved text from `<课程名称>-课程简介.txt`.

## Content rows: upload local files

Create one row for the video and one for the PDF courseware (when courseware should be offered). Upload the prepared local files directly; do not pick files from the material library.

For each row:

- Row name `【视频】<课程名称>` or `【资料】<课程名称>`, matching the filename.
- Exact lecturer identity from the corporate directory; prefer the displayed employee identifier (for example `张三(10001)`). If several people remain, ask the user.
- `学习时长` `1`, `是否可以下载` `否` (defaults above).

Upload route `+上传文件`:

1. After `+新增行`, click the row's `+上传文件` and wait for `素材上传`.
2. In the dialog's `目录`, select the exact academy directory. The menu may stay open: close it by clicking the same directory textbox again. **Do not press Escape**; it can close the whole dialog.
3. With the menu closed, set the file on the dialog's hidden `input[type="file"]` through a file-chooser event; do not click a visible file button while the menu overlay is open.
4. Wait for `分片完成，可以上传`, click `开始上传`, poll until `已上传`, fill the material title, click `保存`, and verify `新增素材成功` plus the filename on the row.
5. If the browser refuses local file access, ask the user to allow the browser extension to access file URLs and restart the browser, then resume the same form. Do not discard it or re-upload blindly.

After each upload, re-read the page and checkpoint row name, lecturer, duration, download setting, and filename.

## Review and publish

Before the final action verify: course name, department, academy/category, introduction, cover preview; and for every row the name, lecturer, `1`-minute duration (unless overridden), `否` download, and uploaded filename; plus visible access, required-learning, recommendation, and topic-course defaults.

With valid explicit authorization, click `提交并发布` once and wait for `新增课程成功`; never resubmit after an error. Search the course list by exact title, confirm `已发布`, record the course ID when shown, and leave the verified result page visible as the deliverable.

## Stage 2 failure handling

- Authentication expired: ask the user to sign in in the same browser session and confirm before resuming.
- Upload stalled: keep the form, record the filename and visible status, and determine from the dialog and row whether it is running, done, or failed. Keep waiting for active uploads; retry only after a confirmed failure; if the outcome cannot be established, report it and ask before uploading another copy.
- After a browser reset, keep existing file associations and rebuild only missing fields. If an association disappeared and the earlier outcome is unknown, ask before uploading again; do not switch to the material library.
- Academy/category blank after a handoff: return control to the user; never guess.
- A success toast is not enough: verify the list record and its status.
