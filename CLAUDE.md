# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Development Tasks

### Running tests
```bash
# Run tests for all Python versions
nox -s tests

# Run tests for a specific Python version
nox -s tests-3.9

# Run a specific test file or pattern
coverage run --append -m unittest discover --buffer -s=tests -p "*_test.py"
```

### Code formatting
```bash
# Format code using black
nox -s format
```

### Installing dependencies
```bash
# Install the package in development mode
pip install -e ".[dev]"

# Install with pipx for command-line usage
pipx install git+https://github.com/googleanalytics/google-analytics-mcp.git
```

### Running the server locally
```bash
# Run the MCP server directly
python -m analytics_mcp.server

# Or use the installed command
google-analytics-mcp
```

## Architecture Overview

This is a Model Context Protocol (MCP) server that provides tools for interacting with Google Analytics APIs. The architecture follows a modular design:

### Core Components

1. **Server Entry Point** (`analytics_mcp/server.py`): 
   - Main entry point that runs the MCP server
   - Imports all tool modules to register them with the coordinator

2. **Coordinator** (`analytics_mcp/coordinator.py`):
   - Creates a singleton FastMCP instance that coordinates all tool registrations
   - All tools register themselves using the `@mcp.tool` decorator

3. **Tool Organization** (`analytics_mcp/tools/`):
   - **admin/info.py**: Tools for Google Analytics Admin API (account summaries, property details, Google Ads links)
   - **reporting/core.py**: Tools for Core Reporting API (dimensions, metrics, reports)
   - **reporting/realtime.py**: Tools for Realtime Reporting API
   - **utils.py**: Shared utilities for API client creation and credential management

### Key Design Patterns

- **Tool Registration**: Tools are automatically registered when their modules are imported in `server.py`
- **Async Architecture**: All tools are async functions using Google's async API clients
- **Credential Management**: Uses Application Default Credentials (ADC) with read-only Analytics scope
- **API Client Factory**: Centralized client creation in `utils.py` with proper authentication and user agent

### Authentication

The server requires Google Analytics read-only scope:
- `https://www.googleapis.com/auth/analytics.readonly`

Credentials are managed through Application Default Credentials (ADC) and can be configured via:
- `gcloud auth application-default login`
- `GOOGLE_APPLICATION_CREDENTIALS` environment variable