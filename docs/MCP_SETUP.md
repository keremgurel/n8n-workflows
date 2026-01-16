# n8n MCP Setup

This project is configured to work with the n8n MCP server for workflow development.

## Configuration

The MCP server is configured locally for this project in `.claude.json` (excluded from git).

### Environment Variables

The following environment variables are configured:
- `N8N_API_URL`: Your n8n instance URL (http://192.168.1.76:5678)
- `N8N_API_KEY`: API key for authentication
- `MCP_MODE`: stdio (required for Claude Code)
- `LOG_LEVEL`: error
- `DISABLE_CONSOLE_OUTPUT`: true

## Setup Instructions

To set up the MCP server for this project:

```bash
claude mcp add n8n-mcp \
  -e N8N_API_URL=http://192.168.1.76:5678 \
  -e N8N_API_KEY=your_api_key_here \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -- npx n8n-mcp
```

## Verify Connection

Check if the MCP server is connected:

```bash
claude mcp list
```

You should see:
```
n8n-mcp: npx n8n-mcp - ✓ Connected
```

## Available Tools

With the MCP server connected, you have access to:
- Node discovery and search
- Workflow creation and management
- Configuration validation
- Template library access
- AI agent workflow tools

## n8n Skills

Additionally, this project has the n8n-mcp-skills installed, providing:
- n8n-workflow-patterns
- n8n-code-javascript
- n8n-code-python
- n8n-expression-syntax
- n8n-validation-expert
- n8n-mcp-tools-expert
- n8n-node-configuration

## References

- [n8n MCP Repository](https://github.com/czlonkowski/n8n-mcp)
- [n8n Skills Repository](https://github.com/czlonkowski/n8n-skills)
