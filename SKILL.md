---
name: douyin-card-cover-extractor
description: Extract and quality-check the designed thumbnail cards shown before Douyin videos are opened. Use for requests about 抖音封面、抖音主页作品缩略图、抖音视频卡片封面、含抖音链接的表格前 N 条封面, or approved delivery of those reviewed Douyin covers to Feishu or Eagle; do not use for other platforms or arbitrary video-frame extraction.
---

# Douyin Card Cover Extractor

Treat “cover” as the designed thumbnail card a viewer sees before opening a video. The target is not a playback frame and not a desktop crop that removes part of the design.

## Establish the batch

- Preserve the source view's current order and the requested row count.
- Extract the corresponding video URLs and keep a stable row-to-video mapping.
- Maintain an auditable manifest containing source row, output filename, video ID, creator, source URL, and QA status.
- Name results with zero-padded row numbers such as `001.jpg` so every file can be traced back to its record.
- Do not silently skip or substitute a record. Report any inaccessible or unmatched video by row number.

## Extract the correct card

Prefer the thumbnail actually used for the matching video in the creator's profile or works list. Match by video ID, not by title alone.

When a browser can load the creator page:

1. Resolve the video's creator profile.
2. Locate the work card whose link contains the same video ID.
3. Read the displayed image URL from the card (`currentSrc` before `src` when available).
4. If the displayed desktop asset is a cropped variant, obtain the corresponding original-scale designed card rather than accepting the crop.

When profile rendering is unavailable, use the video's detail data only after reading [references/douyin-cover-fields.md](references/douyin-cover-fields.md). The desired fallback is the original-scale card design, and it must be visually verified.

Download the image bytes promptly. Do not treat a signed CDN URL as the deliverable because it can expire.

## Quality gate

Review all images, not only a sample. Use a contact sheet for the batch and inspect suspicious files individually.

Reject and retry any result that is:

- blank, nearly solid, extremely dark, or obviously a transition frame;
- missing any title, subject, or composition element that is actually present in the reference card;
- a random video frame, wrong video, or wrong creator;
- visibly cropped compared with the designed card;
- corrupt or unmapped to its source row.

Verify the exact file count, unique row numbering, and decodability. Flag exact or obvious perceptual duplicates and return to the source cards for verification: keep them when the source genuinely reuses the same cover, otherwise retry the mismatched extraction. Mixed 3:4 and 9:16 dimensions are acceptable when they reflect the actual card design; do not crop merely to make dimensions uniform unless the user asks.

## Delivery and later destinations

Unless the user specifies otherwise, create a new clearly named Desktop folder for review and keep earlier attempts intact. Show the reviewed set before changing external systems.

Do not update Feishu or import into Eagle unless the user explicitly approves that named destination. Approval for Feishu does not authorize Eagle, and approval for Eagle does not authorize Feishu. If approval comes later:

- when Feishu is approved, preserve a backup of existing cover values before replacement;
- upload or attach the image bytes instead of leaving expiring CDN URLs;
- verify only the approved destination contains the expected count after the operation.
