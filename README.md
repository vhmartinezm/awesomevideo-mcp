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

## Pricing

Free to connect. `estimate` prices every job for free before it runs. Paid tools use prepaid credits (1 credit = 1¢); failed jobs are refunded automatically. See [pricing](https://awesomevideoeditor.com/pricing).
