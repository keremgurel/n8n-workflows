# Instagram Reel Transcription & Content Analysis Workflow

## Overview
This workflow takes an Instagram username as input, fetches their top 10 most-watched reels, transcribes each video, and generates comprehensive content analysis including summaries, recreated versions, content ideas, and full scripts.

## Workflow Structure

### 1. Input Collection
- **Node**: Webhook or Form Trigger
- **Purpose**: Accept Instagram username input
- **Configuration**: POST endpoint to receive username

### 2. Fetch Top 10 Reels
- **Node**: HTTP Request (Instagram API/Scraper)
- **Purpose**: Retrieve the 10 most-watched reels for the given username
- **Data Needed**:
  - Reel URLs
  - View counts
  - Video file URLs
  - Metadata (title, description, hashtags)

### 3. Download Videos
- **Node**: HTTP Request (Loop)
- **Purpose**: Download each reel video file
- **Configuration**: Iterate through the 10 reels

### 4. Transcribe Videos
- **Node**: OpenAI Whisper API or Alternative Transcription Service
- **Purpose**: Convert video audio to text transcripts
- **Configuration**:
  - Extract audio from video
  - Send to Whisper API
  - Collect transcripts

### 5. AI Processing - Summaries
- **Node**: OpenAI GPT-4 or Claude API
- **Purpose**: Generate summary for each video
- **Prompt**: "Summarize this video transcript concisely, capturing the main points and key messages"

### 6. AI Processing - Recreatable Versions
- **Node**: OpenAI GPT-4 or Claude API
- **Purpose**: Create original, improved versions of each video script
- **Prompt**: "Based on this transcript, create an original, improved version that is more engaging. Expand or shorten as needed for better impact. Make it unique while preserving the core message."

### 7. AI Processing - Content Ideas
- **Node**: OpenAI GPT-4 or Claude API
- **Purpose**: Generate similar content ideas with full scripts
- **Prompt**: "Based on these video themes, generate 5 similar content ideas with complete scripts that would resonate with the same audience"

### 8. Format Document Content
- **Node**: Code Node (JavaScript)
- **Purpose**: Structure all data into Google Docs format with sections:
  1. **Video Summaries**: Summary of each of the 10 reels
  2. **Recreatable Content**: Improved, original versions of each video
  3. **Similar Content Ideas**: New content ideas with scripts
  4. **Appendix**: Full transcripts of all videos

### 9. Create Google Doc
- **Node**: Google Docs API
- **Purpose**: Create formatted document with all sections
- **Configuration**:
  - Create new document
  - Add title: "Instagram Content Analysis - [username]"
  - Insert formatted content with proper headings

### 10. Return Result
- **Node**: Webhook Response or Email
- **Purpose**: Send Google Docs link back to user

## Required Credentials

1. **Instagram API** or third-party scraper service (e.g., Apify, RapidAPI)
2. **OpenAI API Key** (for Whisper transcription and GPT-4)
3. **Google Cloud Project** with Docs API enabled
4. **Google OAuth** credentials for Docs access

## Estimated Processing Time
- Per reel: ~30-60 seconds (download + transcribe + AI processing)
- Total for 10 reels: ~10-15 minutes

## Considerations

### Rate Limits
- Instagram API: May need to respect rate limits
- OpenAI API: Monitor token usage and rate limits
- Google Docs API: Typically generous limits

### Error Handling
- Handle cases where reels can't be accessed
- Retry logic for API failures
- Validate Instagram username exists

### Alternative Approaches

#### Instagram Data Collection
1. **Official Instagram Graph API** (requires business account)
2. **Third-party services**: Apify Instagram Scraper
3. **Puppeteer/Playwright**: Browser automation (slower but reliable)

#### Transcription Services
1. **OpenAI Whisper** (recommended - accurate and affordable)
2. **Assembly AI** (specialized in speech-to-text)
3. **Google Speech-to-Text**
4. **AWS Transcribe**

#### AI Content Generation
1. **OpenAI GPT-4** (most capable for creative content)
2. **Anthropic Claude** (excellent for analysis and writing)
3. **Google Gemini Pro** (good balance of cost and quality)

## Data Flow

```
User Input (Username)
    ↓
Fetch Top 10 Reels (API/Scraper)
    ↓
Download Videos (HTTP Requests)
    ↓
Transcribe Audio (Whisper API)
    ↓
┌─────────────┬─────────────┬─────────────┐
│   Summary   │  Recreate   │ New Ideas   │ (Parallel AI Processing)
└─────────────┴─────────────┴─────────────┘
    ↓
Format Document Structure (Code Node)
    ↓
Create Google Doc (Google Docs API)
    ↓
Return Doc Link to User
```

## Output Format

### Google Doc Structure

```markdown
# Instagram Content Analysis - @username

## 1. Video Summaries

### Video 1: [Title/Description]
- Views: [count]
- Summary: [AI-generated summary]

### Video 2: [Title/Description]
...

## 2. Recreatable Content Versions

### Video 1: Enhanced Script
[Original, improved version with creative enhancements]

### Video 2: Enhanced Script
...

## 3. Similar Content Ideas & Scripts

### Idea 1: [Title]
**Concept**: [description]
**Full Script**: [complete script]

### Idea 2: [Title]
...

## Appendix: Full Transcripts

### Video 1 Transcript
[Complete transcript]

### Video 2 Transcript
...
```

## Next Steps

1. Set up required API credentials in n8n
2. Test Instagram data fetching
3. Implement transcription pipeline
4. Configure AI prompts for optimal output
5. Test end-to-end workflow
6. Optimize for performance and cost
