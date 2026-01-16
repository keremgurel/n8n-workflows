# Instagram Transcription Workflow

## Quick Import

To import this workflow into your n8n instance:

1. Open n8n at `http://192.168.1.76:5678`
2. Click "Import from File"
3. Select `instagram-transcription-workflow.json`
4. Configure the required credentials (see below)

## What It Does

This workflow:
1. ✅ Accepts Instagram username via webhook/form
2. ✅ Fetches top 10 most-watched reels
3. ✅ Downloads each video
4. ✅ Transcribes audio using OpenAI Whisper
5. ✅ Generates AI summaries for each video
6. ✅ Creates improved, original recreated versions
7. ✅ Generates 10 similar content ideas with full scripts
8. ✅ Compiles everything into a formatted Google Doc

## Google Doc Output Structure

```
# Instagram Content Analysis - @username

## 1. Video Summaries
- Video 1-10 with views, URLs, and AI summaries

## 2. Recreatable Content Versions
- Enhanced scripts for each video (with hooks, content, CTAs)

## 3. Similar Content Ideas & Scripts
- 10 new content ideas with complete scripts

## Appendix: Full Transcripts
- All 10 video transcripts
```

## Required Credentials

### 1. RapidAPI (Instagram Scraper)
- Sign up at https://rapidapi.com
- Subscribe to an Instagram scraper API
- Add API key to n8n credentials

### 2. OpenAI API
- Get API key from https://platform.openai.com
- Requires access to:
  - Whisper API (transcription)
  - GPT-4 Turbo (content generation)
- Add to n8n credentials

### 3. Google Docs API
- Create Google Cloud Project
- Enable Google Docs API
- Create OAuth 2.0 credentials
- Add to n8n credentials

## Usage

### Via Webhook

```bash
curl -X POST http://your-n8n-instance/webhook/instagram-analysis \
  -H "Content-Type: application/json" \
  -d '{"username": "example_user"}'
```

### Response

```json
{
  "success": true,
  "document_url": "https://docs.google.com/document/d/...",
  "document_id": "abc123...",
  "username": "example_user",
  "videos_processed": 10
}
```

## Processing Time

- Fetching reels: ~5-10 seconds
- Downloading videos: ~30-60 seconds
- Transcription (10 videos): ~2-5 minutes
- AI processing: ~3-5 minutes
- Document creation: ~10 seconds

**Total**: ~6-11 minutes per run

## Cost Estimate (per run)

- RapidAPI: ~$0.01-0.10 (depends on plan)
- OpenAI Whisper: ~$0.36 (10 videos @ ~1 min each)
- OpenAI GPT-4: ~$0.50-1.00 (summaries + recreations + ideas)
- Google Docs API: Free

**Total**: ~$0.87-1.46 per analysis

## Customization

### Adjust Number of Videos
In "Sort & Get Top 10" node, change `.slice(0, 10)` to your desired number.

### Change AI Model
Replace `gpt-4-turbo-preview` with:
- `gpt-4` (more expensive, slightly better)
- `gpt-3.5-turbo` (cheaper, faster, lower quality)

### Modify Content Ideas Count
In "Generate Content Ideas & Scripts", change "generate 10 similar" to your desired number.

## Troubleshooting

### Instagram API Issues
- **Private accounts**: Cannot access reels from private accounts
- **Rate limits**: May need to implement delays between requests
- **No reels found**: User might not have public reels

### Transcription Errors
- **Language**: Update Whisper language parameter if not English
- **Audio quality**: Poor audio may result in bad transcripts
- **File size**: Very long videos may need chunking

### API Costs
- Monitor OpenAI usage at https://platform.openai.com/usage
- Set spending limits to avoid unexpected charges

## Alternative Services

### Instagram Data
- Apify Instagram Scraper
- Phantombuster
- Manual export (less automated)

### Transcription
- AssemblyAI (specialized, more expensive)
- Google Speech-to-Text
- AWS Transcribe

### AI Generation
- Anthropic Claude (via Claude API)
- Google Gemini Pro
- Local models (slower, free)

## Future Enhancements

- [ ] Add error handling for failed downloads
- [ ] Implement retry logic for API failures
- [ ] Add support for TikTok and YouTube Shorts
- [ ] Include hashtag and trend analysis
- [ ] Generate thumbnail ideas
- [ ] Add competitor analysis
- [ ] Email notification when complete
- [ ] Save to database for history

## Related Documentation

- [Workflow Design Doc](../docs/instagram-transcription-workflow.md)
- [MCP Setup Guide](../docs/MCP_SETUP.md)
