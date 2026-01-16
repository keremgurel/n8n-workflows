# n8n Workflows Repository

This repository contains n8n workflows created using n8n MCP and n8n skills.

## Repository Structure

```
n8n-workflows/
├── workflows/           # Individual workflow JSON files
├── docs/               # Workflow documentation
└── README.md           # This file
```

## Tools & Resources

- [n8n Skills](https://github.com/czlonkowski/n8n-skills)
- [n8n MCP](https://github.com/czlonkowski/n8n-mcp)

## Workflow Creation Process

1. Describe the workflow requirements
2. Workflow will be created using n8n MCP and n8n skills
3. Workflow JSON file will be stored in the `workflows/` directory
4. Documentation will be added to the `docs/` directory

## Getting Started

To use these workflows:
1. Import the workflow JSON file into your n8n instance
2. Configure any required credentials
3. Adjust webhook URLs or trigger settings as needed
4. Activate the workflow

## Workflows

### 1. Instagram Reel Transcription & Content Analysis

**Status**: Ready for implementation
**File**: `workflows/instagram-transcription-template.json`
**Documentation**: [docs/instagram-transcription-workflow.md](docs/instagram-transcription-workflow.md)
**Implementation Guide**: [docs/implementation-guide.md](docs/implementation-guide.md)

**Description**: Analyzes Instagram profiles by fetching top 10 reels, transcribing videos, generating summaries, creating recreated content versions, suggesting similar content ideas with scripts, and compiling everything into a formatted Google Doc.

**Features**:
- Webhook trigger accepts Instagram username
- Fetches top 10 most-watched reels
- Transcribes videos using OpenAI Whisper
- Generates AI-powered summaries
- Creates improved, original content versions
- Suggests 5 similar content ideas with full scripts
- Outputs formatted Google Doc with all sections

**Requirements**:
- OpenAI API key (Whisper + GPT-4)
- Instagram data source (Apify, RapidAPI, or custom)
- Google Docs OAuth2 credentials

**Processing Time**: ~10-15 minutes for 10 videos
