# Implementation Guide: Instagram Transcription Workflow

## Quick Start

1. Import the workflow JSON into your n8n instance
2. Configure required credentials
3. Test with a sample Instagram username
4. Activate the workflow

## Step-by-Step Setup

### 1. Import Workflow

1. Open your n8n instance at `http://192.168.1.76:5678`
2. Click "Add workflow" → "Import from File"
3. Select `workflows/instagram-transcription-template.json`
4. The workflow will be imported with all nodes pre-configured

### 2. Configure Credentials

You'll need to set up the following credentials in n8n:

#### A. OpenAI API (for Whisper & GPT-4)
1. Go to Settings → Credentials → Add Credential
2. Select "OpenAI API"
3. Enter your OpenAI API key
4. Test the connection
5. Save as "OpenAI API"

**Get API Key**: https://platform.openai.com/api-keys

#### B. Google Docs OAuth2
1. Go to Settings → Credentials → Add Credential
2. Select "Google Docs OAuth2 API"
3. Follow the OAuth flow to authorize n8n
4. Save as "Google Docs OAuth2"

**Setup Guide**: https://docs.n8n.io/integrations/builtin/credentials/google/

#### C. Apify API (for Instagram scraping)
1. Go to Settings → Credentials → Add Credential
2. Select "HTTP Header Auth"
3. Add header name: `Authorization`
4. Add header value: `Bearer YOUR_APIFY_TOKEN`
5. Save as "Apify API"

**Get API Token**: https://console.apify.com/account/integrations

**Alternative**: You can use other Instagram scraping services:
- RapidAPI Instagram endpoints
- Bright Data Instagram Scraper
- Custom Puppeteer/Playwright solution

### 3. Update Node Configurations

#### Fetch Instagram Reels Node
The template uses Apify's Instagram Profile Scraper. You may need to adjust:

```javascript
// Current configuration
{
  "url": "https://api.apify.com/v2/acts/apify~instagram-profile-scraper/run-sync-get-dataset-items",
  "body": {
    "usernames": ["username"],
    "resultsType": "posts",
    "resultsLimit": 10
  }
}
```

**If using a different service**, update this node with your chosen API endpoint and parameters.

#### Webhook Trigger
The webhook expects POST requests with this format:

```json
{
  "username": "instagram_username_here"
}
```

**Webhook URL** (after activation):
```
http://192.168.1.76:5678/webhook/instagram-analysis
```

### 4. Test the Workflow

#### Manual Test
1. Click on the "Webhook" node
2. Click "Listen for Test Event"
3. Send a test request:

```bash
curl -X POST http://192.168.1.76:5678/webhook-test/instagram-analysis \\
  -H "Content-Type: application/json" \\
  -d '{"username": "natgeo"}'
```

4. Monitor execution in the workflow editor
5. Check for errors in each node

#### Node-by-Node Testing
Test each node individually before running the full workflow:

1. **Extract Username**: Verify username extraction works
2. **Fetch Reels**: Confirm Instagram API returns data
3. **Download Video**: Check video downloads successfully
4. **Transcribe**: Verify Whisper transcription works
5. **AI Processing**: Test all three AI nodes
6. **Format Document**: Check document structure
7. **Create Google Doc**: Verify doc creation and permissions

### 5. Activate the Workflow

Once testing is complete:
1. Click the toggle at the top right to activate
2. The webhook will now be live and accepting requests

## Usage

### Making a Request

Send a POST request to your webhook:

```bash
curl -X POST http://192.168.1.76:5678/webhook/instagram-analysis \\
  -H "Content-Type: application/json" \\
  -d '{"username": "instagram_username"}'
```

### Response Format

```json
{
  "success": true,
  "document_url": "https://docs.google.com/document/d/...",
  "username": "instagram_username",
  "videos_processed": 10
}
```

### Processing Time

Expect approximately 10-15 minutes for complete processing:
- Fetching reels: ~30 seconds
- Per video processing: ~60-90 seconds × 10
- Document creation: ~10 seconds

## Workflow Details

### Node Flow

1. **Webhook** → Receives username input
2. **Extract Username** → Parses input
3. **Fetch Instagram Reels** → Gets top 10 reels via API
4. **Split Into Reels** → Processes each reel individually
5. **Download Video** → Downloads video file
6. **Transcribe with Whisper** → Converts audio to text
7. **Generate Summary** (parallel) → Creates video summary
8. **Recreate Content** (parallel) → Creates improved version
9. **Combine Results** → Merges summary + recreation
10. **Loop Back** → Processes next video
11. **Aggregate All Videos** → Collects all processed videos
12. **Generate Content Ideas** → Creates 5 new content ideas
13. **Format Google Doc** → Structures document content
14. **Create Google Doc** → Creates formatted document
15. **Respond** → Returns doc URL to user

### Parallel Processing

The workflow uses parallel processing for efficiency:
- **Summary** and **Recreate** nodes run simultaneously after transcription
- This saves ~30 seconds per video

### Loop Processing

The `Split Into Reels` and `Loop Back` nodes handle batch processing:
- Processes videos one at a time
- Aggregates results after all videos complete
- Prevents rate limiting issues

## Troubleshooting

### Common Issues

#### 1. Instagram API Errors
**Problem**: Can't fetch reels or unauthorized errors
**Solutions**:
- Verify Apify API token is correct
- Check Instagram username exists and is public
- Try a different scraping service
- Ensure account has access to target profile

#### 2. Transcription Failures
**Problem**: Whisper API errors or timeouts
**Solutions**:
- Check OpenAI API key and credits
- Verify video has audio track
- Try reducing video file size
- Consider alternative transcription services

#### 3. Rate Limiting
**Problem**: API rate limit exceeded
**Solutions**:
- Add delays between API calls
- Reduce number of videos processed
- Upgrade API plan limits
- Implement retry logic with backoff

#### 4. Google Doc Creation Fails
**Problem**: Permission errors or doc not created
**Solutions**:
- Re-authenticate Google OAuth
- Check Google Drive storage space
- Verify Docs API is enabled in Google Cloud
- Check OAuth scopes include Docs access

#### 5. Memory Issues
**Problem**: Workflow times out or crashes
**Solutions**:
- Process fewer videos per run
- Reduce video quality/size
- Increase n8n memory limit
- Split workflow into smaller chunks

### Error Handling

Add error handling nodes:

1. **After Fetch Reels**: Check if data exists
2. **After Download Video**: Verify file size > 0
3. **After Transcription**: Check transcript length
4. **After Each AI Node**: Verify response received

Example error check (Code node):

```javascript
if (!items[0]?.json?.text) {
  throw new Error('Transcription failed - no text returned');
}
return items;
```

## Optimization Tips

### Cost Optimization

1. **Use GPT-3.5 Turbo** instead of GPT-4 for summaries (cheaper)
2. **Batch AI requests** where possible
3. **Cache Instagram data** to avoid repeated fetches
4. **Compress videos** before transcription

### Performance Optimization

1. **Parallel processing**: Process multiple videos simultaneously
2. **Streaming responses**: Use streaming for AI responses
3. **Webhook queue**: Queue requests if processing takes long
4. **Async processing**: Return webhook response immediately, process in background

### Quality Optimization

1. **Better prompts**: Refine AI prompts for better outputs
2. **Higher temperature**: Increase creativity for content ideas (0.8-0.9)
3. **More examples**: Include few-shot examples in prompts
4. **Post-processing**: Add validation and cleanup steps

## Advanced Customization

### Alternative Instagram Data Sources

#### Option 1: Official Instagram Graph API
```javascript
// Requires Facebook Business Account
{
  "url": "https://graph.instagram.com/v18.0/me/media",
  "headers": {
    "Authorization": "Bearer {access_token}"
  },
  "qs": {
    "fields": "id,caption,media_type,media_url,thumbnail_url,timestamp",
    "limit": 10
  }
}
```

#### Option 2: RapidAPI Instagram Endpoint
```javascript
{
  "url": "https://instagram-scraper-api2.p.rapidapi.com/v1/profile_posts",
  "headers": {
    "X-RapidAPI-Key": "your_key_here",
    "X-RapidAPI-Host": "instagram-scraper-api2.p.rapidapi.com"
  },
  "qs": {
    "username_or_id_or_url": "username"
  }
}
```

### Alternative Transcription Services

#### Option 1: Assembly AI
```javascript
// More accurate for certain accents/languages
{
  "url": "https://api.assemblyai.com/v2/transcript",
  "headers": {
    "authorization": "your_api_key"
  },
  "body": {
    "audio_url": "video_url_here"
  }
}
```

#### Option 2: Google Speech-to-Text
```javascript
// Better for multilingual content
{
  "url": "https://speech.googleapis.com/v1/speech:recognize",
  "headers": {
    "Authorization": "Bearer {access_token}"
  },
  "body": {
    "config": {
      "encoding": "LINEAR16",
      "languageCode": "en-US"
    },
    "audio": {
      "content": "base64_audio"
    }
  }
}
```

### Custom AI Prompts

Modify the AI prompts for different outcomes:

#### More Creative Summaries
```
Create an engaging, creative summary of this video that would work great as a social media caption. Include relevant emojis and hashtags.

Transcript: {{$json.text}}
```

#### SEO-Optimized Content
```
Rewrite this video script for SEO optimization. Include:
1. Target keywords naturally
2. Clear structure with headings
3. Call-to-action at the end

Transcript: {{$json.text}}
```

#### Different Content Format
```
Transform this video content into:
1. A Twitter thread (10 tweets)
2. A LinkedIn post
3. A blog article outline

Transcript: {{$json.text}}
```

## Monitoring & Analytics

### Track Key Metrics

1. **Processing time per video**
2. **API costs per run**
3. **Success/failure rates**
4. **Most common errors**

### Add Monitoring Node

Add a final node to log execution data:

```javascript
// Log to database or external service
return [{
  json: {
    workflow_id: workflow.id,
    username: $json.username,
    videos_processed: $json.videos_processed,
    processing_time: execution.duration,
    timestamp: new Date().toISOString(),
    success: true
  }
}];
```

## Next Steps

1. ✅ Import workflow
2. ✅ Configure credentials
3. ✅ Test with sample data
4. ✅ Activate workflow
5. → Integrate with your app/service
6. → Monitor and optimize
7. → Scale as needed

## Support

For issues or questions:
- Check n8n documentation: https://docs.n8n.io
- n8n community forum: https://community.n8n.io
- This repository's issues: https://github.com/keremgurel/n8n-workflows/issues
