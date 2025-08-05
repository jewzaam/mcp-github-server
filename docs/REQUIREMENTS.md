# MCP GitHub Server Requirements

## Executive Summary

Transform the existing `gh-pulls-summary` tool into a Model Context Protocol (MCP) server that provides comprehensive GitHub repository analysis capabilities. Reuse existing code within this repository while adding structured MCP tools for AI coding assistants.

## Core Principles

- **Single Repository Focus**: Designed for local repo context, not cross-repo analysis
- **PR Comment Analysis**: Optimized for LLM analysis of PR comments and review states
- **Code Reuse**: Preserve existing `gh-pulls-summary` functionality, especially review state logic
- **Read-Only Security**: MCP server grants no mutation permissions, enhancing AI tool trust
- **Review State Accuracy**: Preserve complex logic for latest review states (approved → changes requested, etc.)

## Architecture and Configuration

### Project Structure
```
mcp_github_server/
├── server.py                 # Main MCP server entry point
├── config.py                 # Configuration and authentication
├── github_client.py          # GitHub API client (reused gh-pulls-summary code)
├── tools/ [repository, pull_request, search, relationship]
└── tests/ [unit, integration]
```

### Configuration and Authentication
```yaml
# mcp_github_server.yaml
github:
  token: "${GITHUB_TOKEN}"           # Bearer token authentication
  base_url: "https://api.github.com" # GitHub Enterprise support
  timeout: 30
  rate_limit_delay: 1

logging:
  level: "INFO"
```

**Authentication Methods**:
- Bearer token authentication using `GITHUB_TOKEN` or `GITHUB_BEARER_TOKEN` environment variable
- If no token provided, requests are unauthenticated (public repositories, 60 requests/hour limit)

## Common Patterns

### Standard Parameters
**Repository Identifier:**
- `owner` (string): Repository owner/organization
- `repo` (string): Repository name

**Filtering Options:**
- `max_results` (int, optional): Maximum results (1-100, default: 25)
- `state` (string, optional): Resource state ("open", "closed", "all")
  - **Note**: GitHub API only has "open"/"closed" states. "closed" includes resolved, merged PRs, etc.

**Content Options:**
- `ref` (string, optional): Branch/commit reference (default: default branch)
- `path` (string, optional): File/directory path

### Standard Response Format
**Pagination (required for all list results):**
```json
{
  "data": [...],
  "pagination": {
    "current_page": 1,
    "has_next_page": true,
    "total_pages": 5,        // Optional, if known
    "per_page": 25,
    "total_count": 120       // Optional, if available from API
  }
}
```

## MCP Tools Specification

### Core Tools (for single repo context)

#### `search_pull_requests`
Search PRs in current repository + *standard repository identifier* + *filtering options* + draft/file/author filters
- Returns: PR list with basic metadata, review counts, approval status

#### `search_issues` 
Search issues in current repository + *standard repository identifier* + *filtering options* + labels/assignee/author filters
- Returns: Issue list with metadata, comment counts, resolution status

#### `get_pull_request_reviews` (reused from `gh-pulls-summary`)
Get comprehensive review analysis for specific PR + *standard repository identifier* + `pr_number`
- **Who has approved**: List of usernames with approval timestamps (latest state only, handles approval → changes → approval flow)
- **Who requested changes**: List of usernames (latest state only, handles approval → changes → approval flow)
- **Changes still requested**: Count of active change requests  
- **Total approvals**: Count of current approvals
- **Review timeline**: Complete review state transitions

#### `get_pull_request_comments`
Get all comments for specific PR + *standard repository identifier* + `pr_number` + resolution filter
- **Resolution filter**: "all", "open", "resolved"
- Returns: Comments with author, timestamp, thread context, resolution status

#### `get_issue_comments`
Get all comments for specific issue + *standard repository identifier* + `issue_number`
- Returns: Comments with author, timestamp, content

#### `get_file_content`
Retrieve repository file content + *standard repository identifier* + *content options* + optional line ranges
- For LLM context when analyzing PR changes

## Code Reuse from `gh-pulls-summary`

**Critical Components to Reuse:**
- **Review state logic**: Complex handling of review transitions (approved → changes → approved)
- **GitHub API client**: `github_api_request`, authentication, error handling, rate limiting
- **PR processing functions**: Filtering, data extraction, review analysis
- **User review mapping**: Latest review state per user (lines 508-527 in original code)
- **Git repository detection**: `get_repo_and_owner_from_git` for local repo context

Makefile targets to retain:
- help
- setup
- test
- coverage
- lint
- install-cursor
along with dependent targets.


## Development Requirements

### Testing Strategy
- **Unit Tests**: 95%+ coverage, fast execution with mocked APIs
- **Integration Tests**: Real GitHub API testing with rate limit awareness  
- **Test Structure**: `tests/{unit,integration,fixtures}`


## Implementation Details

### Error Handling (reused from `gh-pulls-summary`)
- Rate limiting detection and graceful handling
- Authentication failure guidance
- Network error retry logic
- MCP-specific: Pydantic validation, proper error response formatting

### Security (Read-Only Design)
- **No mutation permissions**: MCP server only reads GitHub data, enhancing AI tool trust
- **Minimal API scopes**: Only requires read access to repositories, issues, and pull requests
- **Authentication**: Bearer token (Personal Access Token), no-auth for public repos
- **No credential storage**: Environment variables only, no secrets persisted
- **Local repo context**: Designed for use within existing repository checkouts

### Documentation
- **User**: README.md, docs/{TOOLS,CONFIGURATION,EXAMPLES}.md

### Installation
```bash
pip install -e .
cp example_mcp_github_server.yaml mcp_github_server.yaml
python -m mcp_github_server.server --config mcp_github_server.yaml
```

### Success Criteria
- **Functional**: Core PR/issue analysis capabilities focused on single repository use case
- **Review State Accuracy**: Reliable tracking of complex review transitions (approved → changes → approved)
- **Comment Analysis**: Complete access to all PR/issue comments for LLM context
- **Quality**: 95%+ test coverage, complete documentation, linting compliance
- **Security**: Read-only operations enhance AI tool trustworthiness