# AwesomeVideo MCP

**Video editing API for AI agents: captions, translation, dubbing in 17 languages, 9:16 shorts.**

AwesomeVideo is a remote MCP server (Streamable HTTP) that lets agents caption, transcribe, translate and dub videos, cut vertical shorts with face tracking, remove silences and write titles, descriptions and hashtags. REST, MCP and OpenAPI share one key and one balance.

[Website](https://awesomevideoeditor.com) · [Pricing](https://awesomevideoeditor.com/pricing) · [llms.txt](https://api.awesomevideoeditor.com/llms.txt) · [OpenAPI](https://api.awesomevideoeditor.com/openapi.json) · MCP Registry: `com.awesomevideoeditor/awesomevideo`

## Endpoints

| Endpoint | Auth |
|---|---|
| `https://api.awesomevideoeditor.com/mcp/oauth` | OAuth 2.1 (PKCE, dynamic client registration, magic-link sign-in) |
| `https://api.awesomevideoeditor.com/mcp` | API key (`Authorization: Bearer YOUR_API_KEY`) |

Get a key at [awesomevideoeditor.com](https://awesomevideoeditor.com), or call the `create_key` tool for a free 0-credit key with no sign-in.

## Connect

**Claude Desktop / Cursor (OAuth)**

```json
{
  "mcpServers": {
    "awesomevideo": {
      "url": "https://api.awesomevideoeditor.com/mcp/oauth"
    }
  }
}
```

**Any MCP client (API key)**

```json
{
  "mcpServers": {
    "awesomevideo": {
      "url": "https://api.awesomevideoeditor.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

## Recipes (copy and paste)

Also at https://awesomevideoeditor.com/agents.

Zero to first video: 1) get a key (`POST /v1/keys/anonymous` or MCP `create_key`, no sign-in); 2) call the recipe with a public video URL; 3) if the balance is short you get 402 `insufficient_balance` with `checkoutUrl` (a human pays $5 = 500 credits for this key, no sign-in) and `quote`; retry the same call after paying. New keys may include a few `welcomeCredits`.

```bash
curl -X POST https://api.awesomevideoeditor.com/v1/keys/anonymous -H "Content-Type: application/json" -d '{"name":"my-agent"}'
# -> {"token":"av_live_...","balanceCents":0,...}  export AV_KEY=av_live_...
```

MCP client config (Streamable HTTP):

```json
{
  "mcpServers": {
    "awesomevideo": {
      "url": "https://api.awesomevideoeditor.com/mcp",
      "headers": {
        "Authorization": "Bearer av_live_..."
      }
    }
  }
}
```

### Caption a video

Video in, MP4 with burned subtitles plus SRT and VTT out. One call. Price: 5 credits per audio minute.

```bash
curl -X POST https://api.awesomevideoeditor.com/v1/captions \
  -H "Authorization: Bearer $AV_KEY" -H "Content-Type: application/json" \
  -d '{"sourceUrl":"https://example.com/talk.mp4","preset":"hormozi-pop","wait":true}'
```

MCP: `caption_video {"sourceUrl":"https://example.com/talk.mp4","preset":"hormozi-pop"}`

```python
import os, requests

r = requests.post(
    "https://api.awesomevideoeditor.com/v1/captions",
    headers={"Authorization": f"Bearer {os.environ['AV_KEY']}"},
    json={
        "sourceUrl": "https://example.com/talk.mp4",
        "preset": "hormozi-pop",
        "wait": True
    },
    timeout=120,
)
print(r.status_code, r.json())
```

### Translate and dub

Spoken translation fitted to the original timing, mixed over the original audio. Price: 5 credits per minute per language, plus transcription and translation.

```bash
curl -X POST https://api.awesomevideoeditor.com/v1/dub \
  -H "Authorization: Bearer $AV_KEY" -H "Content-Type: application/json" \
  -d '{"mediaUrl":"https://example.com/talk.mp4","targetLanguage":"es"}'
```

MCP: `dub {"mediaUrl":"https://example.com/talk.mp4","targetLanguage":"es"}`

```python
import os, requests

r = requests.post(
    "https://api.awesomevideoeditor.com/v1/dub",
    headers={"Authorization": f"Bearer {os.environ['AV_KEY']}"},
    json={
        "mediaUrl": "https://example.com/talk.mp4",
        "targetLanguage": "es"
    },
    timeout=120,
)
print(r.status_code, r.json())
```

### 9:16 shorts from a long video

An LLM picks the best moments, reframes them to 9:16 on the speaker and captions them. Price: about 1 credit per source minute plus a few per clip (30 min, 3 clips of 60 s = 42 credits); quote first.

```bash
curl -X POST https://api.awesomevideoeditor.com/v1/clips \
  -H "Authorization: Bearer $AV_KEY" -H "Content-Type: application/json" \
  -d '{"sourceUrl":"https://example.com/podcast.mp4","count":3,"reframe":"face","platform":"tiktok"}'
```

MCP: `create_clips {"sourceUrl":"https://example.com/podcast.mp4","count":3,"reframe":"face","platform":"tiktok"}`

```python
import os, requests

r = requests.post(
    "https://api.awesomevideoeditor.com/v1/clips",
    headers={"Authorization": f"Bearer {os.environ['AV_KEY']}"},
    json={
        "sourceUrl": "https://example.com/podcast.mp4",
        "count": 3,
        "reframe": "face",
        "platform": "tiktok"
    },
    timeout=120,
)
print(r.status_code, r.json())
```

### Caption a folder

Up to 50 videos in one call, one style, priced together. dryRun quotes for free. Price: 5 credits per audio minute per video.

```bash
curl -X POST https://api.awesomevideoeditor.com/v1/batch \
  -H "Authorization: Bearer $AV_KEY" -H "Content-Type: application/json" \
  -d '{"operation":"caption","preset":"hormozi-pop","items":[{"mediaUrl":"https://example.com/ep1.mp4"},{"mediaUrl":"https://example.com/ep2.mp4"}]}'
```

MCP: `batch {"operation":"caption","preset":"hormozi-pop","items":[{"mediaUrl":"https://example.com/ep1.mp4"},{"mediaUrl":"https://example.com/ep2.mp4"}]}`

```python
import os, requests

r = requests.post(
    "https://api.awesomevideoeditor.com/v1/batch",
    headers={"Authorization": f"Bearer {os.environ['AV_KEY']}"},
    json={
        "operation": "caption",
        "preset": "hormozi-pop",
        "items": [
            {
                "mediaUrl": "https://example.com/ep1.mp4"
            },
            {
                "mediaUrl": "https://example.com/ep2.mp4"
            }
        ]
    },
    timeout=120,
)
print(r.status_code, r.json())
```

## Tools (27)

| Tool | What it does |
|---|---|
| `create_key` | Create a free API key, no sign-in |
| `upload` | Upload a video or audio file |
| `import_url` | Import media from a direct URL |
| `caption_video` | Transcribe, optionally translate, and burn styled captions in one call |
| `transcribe` | Speech to captions (SRT, VTT or JSON with word timestamps) |
| `edit_transcript` | Correct cue text or timing without transcribing again |
| `burn_captions` | Burn existing captions into a video with a preset |
| `get_catalog` | Read capabilities, limits and prices |
| `estimate` | Price any job or pipeline for free |
| `preview` | See captions on the real video before paying for a burn |
| `get_presets` | List caption style presets |
| `manage_preset` | Create or update a brand preset |
| `batch` | Run one operation on 1–50 videos in one call |
| `translate` | Translate captions, keeping cue timing |
| `dub` | Dub a video into another language, timed to the original |
| `create_clips` | Cut 1–5 vertical 9:16 shorts with face-tracking reframe |
| `generate_metadata` | Titles, description, hashtags and YouTube chapters |
| `remove_silences` | Cut silences and filler words |
| `request_review` | Send a human review link before burning |
| `get_review` | Check the status of a human review |
| `get_glossary` | Read the saved transcription glossary |
| `update_glossary` | Update spellings and aliases for transcription |
| `get_job` | Read any job or batch |
| `list_jobs` | List recent jobs |
| `resume_job` | Continue a failed job from where it stopped |
| `create_checkout` | Get a checkout link to add credits |
| `get_account` | Read balance and account details |

## Agent skill (ClawHub)

ClawHub-ready skill for agents: [`skills/awesomevideo-captions/SKILL.md`](skills/awesomevideo-captions/SKILL.md) — keyless `/mcp` + `create_key`, one-call `caption_video` / REST captions.

## Pricing

Free to connect. `estimate` prices every job for free before it runs. Paid tools use prepaid credits (1 credit = 1¢); failed jobs are refunded automatically. See [pricing](https://awesomevideoeditor.com/pricing).
