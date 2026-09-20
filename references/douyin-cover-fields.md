# Douyin cover field notes

Read this reference when the creator profile cannot be rendered and extraction must use video-detail data.

## Field selection

The visible names are deceptively similar:

| Field | Typical meaning | Use as final cover? |
| --- | --- | --- |
| `video.cover_original_scale` | Original-scale designed card/poster, commonly the vertical card with title and subject | Preferred API fallback after visual verification |
| `video.cover` | Desktop/web crop, often about 4:3 and capable of cutting off the card design | Only if it visibly matches the requested card and is not cropped |
| `video.origin_cover` | Original playback cover or video frame; may be blank, textless, or a different moment | No |
| `video.dynamic_cover` | Animated or alternate cover resource | Do not use as a static final cover without verification |

The profile or works-list card remains the source of truth. Field behavior can change, so verify the downloaded pixels rather than trusting metadata dimensions or field names.

## Practical checks

- Match the detail response's video ID to the source row before reading a cover field.
- Prefer the first working URL in the appropriate `url_list`, then verify the decoded image.
- Signed URLs can include `x-expires`; download the file rather than saving only the URL.
- Do not rewrite a signed URL's resize template to request another size. Signatures are commonly bound to the path and altered URLs can return 403.
- If the original-scale card is absent or still wrong, return to the creator's profile and locate the matching video card. Do not fall back to `origin_cover` merely to complete the count.
