# n8n MCP Configuration

This directory contains the MCP (Model Context Protocol) configuration for working with n8n workflows.

## Setup

The MCP server is already configured in your Claude Desktop config at:
`~/Library/Application Support/Claude/claude_desktop_config.json`

## Configuration

The `config.json` file in this directory contains the same configuration for reference and portability.

### Environment Variables

- `N8N_API_URL`: Your n8n instance URL (e.g., http://192.168.1.76:5678)
- `N8N_API_KEY`: Your n8n API key for authentication
- `MCP_MODE`: Set to "stdio" for Claude Desktop integration
- `LOG_LEVEL`: Set to "error" to reduce noise

## Usage

The n8n MCP provides tools for:
- Searching for n8n nodes
- Validating node configurations
- Creating and updating workflows
- Deploying templates
- Managing workflow executions

See the n8n-skills documentation for detailed usage instructions.
