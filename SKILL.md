# SKILL: AI Reels — Clean Explainer (starter workflow)

**Purpose:** produce a finished animated 9:16 explainer reel from a one-line chat request, using AI image generation, image-to-video animation, and ffmpeg assembly.

## Triggers (read intent, not exact words)
- `Explainer reel: <topic>` → styles/clean-explainer.md
- `Auto reel: <topic>` → same lane, fully hands-off: AI-cloned-voice VO per rule 1 AUTO mode
- If the message includes a URL: FETCH it first, extract real facts, never invent claims.

## Hard rules (apply to every reel)
1. **VO mode switch (two tiers).**
   - **DEFAULT = the creator's real recorded voice.** They record after script approval (any decent mic; a phone works). The cleanup chain handles the rest.
   - **AUTO = an ElevenLabs clone of the creator's voice** — only when the request says `auto reel:`. Generate the VO **per line** (one API call per scene → exact per-scene durations, no silence-detection sync needed). TTS is already clean: skip noise/reverb repair, apply only a light pacing tweak (~1.07×).
   - Never a generic stock AI voice — the creator's voice (real or cloned) is the brand.
2. **Text baked into images.** Any headline/label/CTA is generated INTO the art by the image model, with the exact words in quotes in the prompt. NEVER add text in post. Text only on beats that need it (hook, key number, CTA); leave in-between shots clean. Verify spelling on every returned frame before animating.
3. **NO sound effects.** VO + one calm music bed only, ~0.12–0.15 volume under the voice, gentle fade on the last 1.5s.
4. **Approval gates:** (a) script + beat sheet → wait for OK; (b) all keyframes → wait for OK; only then animate. Never spend video credits before keyframe approval.
5. **Animation settings:** set the aspect ratio EXPLICITLY on every video-gen call ("9:16" — most models default to 16:9), turn OFF generated audio, and prompt "all typography stays completely static and crisp; only subtle motion in <background elements>".
6. **Consistency:** pick 3–4 colors and one visual style suffix, and reuse them on every frame. If a recurring character appears, attach the same reference image on every scene with "reproduce the referenced character EXACTLY."
7. **Pace:** if the finished reel feels slow, speed the WHOLE thing 1.07–1.10× (`setpts=PTS/S` + `atempo=S`) — pitch stays natural.
8. **VO editing (real-voice mode):** the VO is the master clock. Cut retakes at silence boundaries — never mid-word, never clip line endings. Chain: `highpass=80, afftdn, deesser, acompressor, loudnorm I=-14`.
9. **Claims discipline:** state facts verified from primary sources; "helps" not "guarantees."

## Workflow
1. **Script + beat sheet** — 8–11 short spoken lines, one per scene, ~45–60s total. One idea per line, everyday words. End on a CTA ("follow for more" or a comment keyword). Send for approval.
2. **Keyframes** — one 9:16 image per scene at high quality, style suffix from the lane doc, text baked where needed. Present all frames for approval; regenerate only the flagged ones.
3. **Animate** — image-to-video per rule 5, ~5s per clip, subtle motion. Poll until all complete.
4. **Get the VO** — DEFAULT: the creator records the approved lines in order, short pause between lines; detect line boundaries by silence. AUTO: generate per-line clone VO (rule 1).
5. **Assemble (ffmpeg)** — per scene: scale/crop to 720×1280, stretch the clip to that line's duration (`setpts`), 30fps; concat all scenes; mux VO + music (`amix`, `loudnorm I=-14`); apply the speed-up if needed.
6. **QA** — extract 2–3 frames (hook, middle, CTA): check spelling, style consistency, audio present, duration. Fix before delivering.
7. **Deliver** — `REEL-<topic>.mp4` + a caption + hashtags back in the chat.

## Caption template
Hook restated in one line → what the thing does (plain words) → CTA → "follow for more" → 8–10 niche hashtags.

## Self-improvement
When the creator gives style feedback, update the relevant rule here or in the lane doc so the next reel starts from the improved recipe.

