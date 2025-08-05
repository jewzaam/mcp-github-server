# MCP GitHub Server Test Plan

This document maps 1:1 to unit tests in the codebase. Each test item corresponds to a specific test method for the project. Validation is done by a human, checking that implementation meets intent of the test, not just technically the literal interpretation of the description.

## CONFIG - Configuration & Authentication

| Test ID | Description | Validated |
|---------|-------------|-----------|
| CONFIG-01 | Load configuration from YAML file with valid settings |  |
| CONFIG-02 | Load configuration from environment variables when no file exists |  |
| CONFIG-03 | Use token authentication when GITHUB_TOKEN is provided |  |
| CONFIG-04 | Use token authentication when GITHUB_BEARER_TOKEN is provided |  |
| CONFIG-05 | Fall back to unauthenticated requests when no token provided |  |
| CONFIG-06 | Configure logging level from config settings |  |
| CONFIG-07 | Handle invalid YAML configuration file gracefully |  |
| CONFIG-08 | Use default values when configuration sections are missing |  |

## CLIENT - GitHub API Client

| Test ID | Description | Validated |
|---------|-------------|-----------|
| CLIENT-01 | Make successful authenticated API request with bearer token |  |
| CLIENT-02 | Make successful unauthenticated API request |  |
| CLIENT-03 | Handle pagination automatically when use_paging=True |  |
| CLIENT-04 | Return single page when use_paging=False |  |
| CLIENT-05 | Handle GitHub API rate limit (403) response gracefully |  |
| CLIENT-06 | Handle GitHub API not found (404) response |  |
| CLIENT-07 | Handle network timeout errors |  |
| CLIENT-08 | Handle invalid JSON response from API |  |
| CLIENT-09 | Detect repository info from local git remote |  |
| CLIENT-10 | Apply rate limit delay between requests |  |
| CLIENT-11 | Pagination is enabled by default if use_paging is not specified |  |

## PRTOOLS - Pull Request Tools

| Test ID | Description | Validated |
|---------|-------------|-----------|
| PRTOOLS-01 | Search pull requests with no filter |  |
| PRTOOLS-02 | Search pull requests with state filter (open/closed/all) |  |
| PRTOOLS-03 | Search pull requests with max_results limit |  |
| PRTOOLS-04 | Return paginated pull request results with metadata |  |
| PRTOOLS-05 | Get pull request reviews with latest state per user |  |
| PRTOOLS-06 | Handle complex review transitions (approved → changes → approved) |  |
| PRTOOLS-07 | Count approvals and change requests correctly |  |
| PRTOOLS-08 | Get pull request comments with resolution filtering (all) |  |
| PRTOOLS-09 | Get pull request comments with resolution filtering (open) |  |
| PRTOOLS-10 | Get pull request comments with resolution filtering (resolved) |  |
| PRTOOLS-11 | Include both issue comments and review comments |  |
| PRTOOLS-12 | Handle non-existent pull request gracefully |  |

## ISSUES - Issue Tools

| Test ID | Description | Validated |
|---------|-------------|-----------|
| ISSUES-01 | Search issues with no filter |  |
| ISSUES-02 | Search issues with state filter (open/closed/all) |  |
| ISSUES-03 | Search issues with labels filter |  |
| ISSUES-04 | Search issues with assignee filter |  |
| ISSUES-05 | Search issues with creator filter |  |
| ISSUES-06 | Return paginated issue results with metadata |  |
| ISSUES-07 | Get issue comments with author and timestamp information |  |
| ISSUES-08 | Handle non-existent issue gracefully |  |
| ISSUES-09 | Handle latest state tracking for reopened issues |  |

## FILES - File Content Tools

| Test ID | Description | Validated |
|---------|-------------|-----------|
| FILES-01 | Get file content from default branch |  |
| FILES-02 | Get file content from specific branch/tag/commit |  |
| FILES-03 | Get file content with line range specification |  |
| FILES-04 | Handle non-existent file path gracefully |  |
| FILES-05 | Handle binary file content appropriately |  |
| FILES-06 | Return base64 encoded content when requested |  |

## SERVER - MCP Server Core

| Test ID | Description | Validated |
|---------|-------------|-----------|
| SERVER-01 | Initialize MCP server with correct name (mcp_github_server) |  |
| SERVER-02 | Register all 6 MCP tools successfully |  |
| SERVER-03 | Handle missing MCP dependencies gracefully |  |
| SERVER-04 | Configure logging on server startup |  |
| SERVER-05 | Initialize GitHub client with configuration |  |
| SERVER-06 | Parse command line arguments correctly |  |

## PAGINATION - Pagination & Response Format

| Test ID | Description | Validated |
|---------|-------------|-----------|
| PAGINATION-01 | Include current_page in all list responses |  |
| PAGINATION-02 | Include has_next_page boolean in all list responses |  |
| PAGINATION-03 | Include per_page count in all list responses |  |
| PAGINATION-04 | Include total_count when available from API |  |
| PAGINATION-05 | Handle last page correctly (has_next_page=false) |  |
| PAGINATION-06 | Respect max_results parameter across all tools |  |

## ERRORS - Error Handling

| Test ID | Description | Validated |
|---------|-------------|-----------|
| ERRORS-01 | Handle GitHub API authentication errors (401) |  |
| ERRORS-02 | Handle GitHub API permission errors (403) |  |
| ERRORS-03 | Handle GitHub API not found errors (404) |  |
| ERRORS-04 | Handle network connection errors |  |
| ERRORS-05 | Handle malformed API responses |  |
| ERRORS-06 | Handle missing required parameters in tool calls |  |
| ERRORS-07 | Handle invalid repository owner/name combinations |  |
| ERRORS-08 | Provide helpful error messages for common issues |  |

