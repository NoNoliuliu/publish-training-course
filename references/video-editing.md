# Verified FFmpeg course-video route

Use this reference only when the user explicitly requests video trimming, segment extraction, or border cropping. The default course workflow preserves the complete source timeline.

## Tool discovery

Check the actual runtime before building a fallback:

1. Use `ffmpeg` and `ffprobe` from `PATH`. On this macOS environment they are installed natively (arm64, no Rosetta) at `~/.local/bin/ffmpeg` and `~/.local/bin/ffprobe`.
2. If a restricted shell does not expose them on `PATH`, probe those two absolute paths directly. Legacy locations still exist as fallbacks and must not be deleted: `~/.workbuddy/bin/ffmpeg` (x86_64, runs under Rosetta) and the `imageio_ffmpeg` binary bundled in the workspace Python venv.
3. Only after confirming that no usable FFmpeg exists should another media backend be considered. AVFoundation has failed to decode/export some otherwise valid training recordings, so it is a fallback rather than the preferred route.
4. Installing a new codec or tool still requires authorization.

## Default no-cut mode

Unless the user explicitly requests an edit, keep the complete original video timeline and content. Do not trim, split, remove footage, crop borders, or apply filters. Write any derived output beside the source files in the original course folder, preserve the original recording, and avoid re-encoding when the source already meets the target compatibility requirements.

## Default timeline trim

For a user-supplied range, compute `duration = end - start` and keep the range authoritative. Put `-ss` before `-i`: FFmpeg seeks to a nearby keyframe and, during re-encoding, decodes and discards frames up to the requested boundary. This is substantially faster than decoding from the beginning while retaining accurate visible boundaries.

Template:

```bash
FF="$(command -v ffmpeg || echo "$HOME/.local/bin/ffmpeg")"
FP="$(command -v ffprobe || echo "$HOME/.local/bin/ffprobe")"
SRC='/absolute/path/source.mov'
OUT='/absolute/path/distinct-output.mp4'

"$FF" -nostdin -hide_banner -loglevel warning -n \
  -ss HH:MM:SS -i "$SRC" -t HH:MM:SS \
  -map 0:v:0 -map 0:a:0 \
  -c:v libx264 -preset veryfast -crf 21 -pix_fmt yuv420p \
  -c:a aac -b:a 128k -movflags +faststart \
  "$OUT"
```

Use `"$FP"` (ffprobe) to read source metadata, codecs, frame rate, audio layout, exact duration, and the start/middle/end verification frames.

Inspect the source before deciding whether frame-rate, sample-rate, or channel normalization is necessary. Omit forced normalization when the source is already compatible. If normalization is required, derive the values from the source and target constraints rather than copying a fixed setting from another course.

When cropping, add the verified crop/filter chain to this re-encoding route. Do not crop when the user chose `不裁边框`.

## Optional stream copy

`-c copy` can finish in seconds and avoids generation loss, but non-keyframe cuts may rely on MP4 edit lists or pre-roll behavior. Different players and upload systems can expose extra duration, leading frames, or boundary differences. Use stream copy only after source-specific checks show that:

- the requested start and end are visibly correct;
- output duration is acceptable;
- audio begins at the correct point and remains aligned;
- full decoding succeeds; and
- the intended training-system/player path honors the resulting timestamps/edit list.

If any check is uncertain, use the default `libx264`/AAC route.

## Required verification

1. Read the output duration, dimensions, frame cadence, and video/audio codecs.
2. Compare output start and end frames with the source at the requested boundaries; inspect a middle frame as a general visual check.
3. Confirm that audio exists and begins at the intended boundary.
4. Run a complete decode, for example:

```bash
"$FF" -nostdin -hide_banner -v error -xerror -i "$OUT" -map 0 -f null -
```

Report only the checks actually performed. A successful metadata read alone is not a complete-decode result.
