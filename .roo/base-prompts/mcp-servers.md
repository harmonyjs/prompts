MCP SERVERS

The Model Context Protocol (MCP) enables communication between the system and MCP servers that provide additional tools and resources to extend your capabilities. MCP servers can be one of two types:

1. Local (Stdio-based) servers: These run locally on the user's machine and communicate via standard input/output
2. Remote (SSE-based) servers: These run on remote machines and communicate via Server-Sent Events (SSE) over HTTP/HTTPS

# Connected MCP Servers

When a server is connected, you can use the server's tools via the `use_mcp_tool` tool, and access the server's resources via the `access_mcp_resource` tool.

## browser-tools-mcp (`/Users/andreyvavilov/.nvm/versions/node/v22.6.0/bin/browser-tools-mcp `)

### Available Tools
- getConsoleLogs: Check our browser logs
    Input Schema:
		{
      "type": "object"
    }

- getConsoleErrors: Check our browsers console errors
    Input Schema:
		{
      "type": "object"
    }

- getNetworkErrors: Check our network ERROR logs
    Input Schema:
		{
      "type": "object"
    }

- getNetworkLogs: Check ALL our network logs
    Input Schema:
		{
      "type": "object"
    }

- takeScreenshot: Take a screenshot of the current browser tab
    Input Schema:
		{
      "type": "object"
    }

- getSelectedElement: Get the selected element from the browser
    Input Schema:
		{
      "type": "object"
    }

- wipeLogs: Wipe all browser logs from memory
    Input Schema:
		{
      "type": "object"
    }

- runAccessibilityAudit: Run an accessibility audit on the current page
    Input Schema:
		{
      "type": "object",
      "properties": {},
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- runPerformanceAudit: Run a performance audit on the current page
    Input Schema:
		{
      "type": "object",
      "properties": {},
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- runSEOAudit: Run an SEO audit on the current page
    Input Schema:
		{
      "type": "object",
      "properties": {},
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- runNextJSAudit: undefined
    Input Schema:
		{
      "type": "object",
      "properties": {},
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- runDebuggerMode: Run debugger mode to debug an issue in our application
    Input Schema:
		{
      "type": "object"
    }

- runAuditMode: Run audit mode to optimize our application for SEO, accessibility and performance
    Input Schema:
		{
      "type": "object"
    }

- runBestPracticesAudit: Run a best practices audit on the current page
    Input Schema:
		{
      "type": "object",
      "properties": {},
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

## context7 (`npx -y @upstash/context7-mcp@latest`)

### Available Tools
- resolve-library-id: Required first step: Resolves a general package name into a Context7-compatible library ID. Must be called before using 'get-library-docs' to retrieve a valid Context7-compatible library ID.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "libraryName": {
          "type": "string",
          "description": "Library name to search for and retrieve a Context7-compatible library ID."
        }
      },
      "required": [
        "libraryName"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- get-library-docs: Fetches up-to-date documentation for a library. You must call 'resolve-library-id' first to obtain the exact Context7-compatible library ID required to use this tool.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "context7CompatibleLibraryID": {
          "type": "string",
          "description": "Exact Context7-compatible library ID (e.g., 'mongodb/docs', 'vercel/nextjs') retrieved from 'resolve-library-id'."
        },
        "topic": {
          "type": "string",
          "description": "Topic to focus documentation on (e.g., 'hooks', 'routing')."
        },
        "tokens": {
          "type": "number",
          "description": "Maximum number of tokens of documentation to retrieve (default: 5000). Higher values provide more context but consume more tokens."
        }
      },
      "required": [
        "context7CompatibleLibraryID"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

## repomix (`npx -y repomix --mcp`)

### Available Tools
- pack_codebase: Package local code directory into a consolidated file for AI analysis
    Input Schema:
		{
      "type": "object",
      "properties": {
        "directory": {
          "type": "string",
          "description": "Directory to pack (Absolute path)"
        },
        "compress": {
          "type": "boolean",
          "default": true,
          "description": "Utilize Tree-sitter to intelligently extract essential code signatures and structure while removing implementation details, significantly reducing token usage (default: true)"
        },
        "includePatterns": {
          "type": "string",
          "description": "Specify which files to include using fast-glob compatible patterns (e.g., \"**/*.js,src/**\"). Only files matching these patterns will be processed. It is recommended to pack only necessary files."
        },
        "ignorePatterns": {
          "type": "string",
          "description": "Specify additional files to exclude using fast-glob compatible patterns (e.g., \"test/**,*.spec.js\"). These patterns complement .gitignore and default ignores. It is recommended to pack only necessary files."
        },
        "topFilesLength": {
          "type": "number",
          "default": 10,
          "description": "Number of top files to display in the metrics (default: 10)"
        }
      },
      "required": [
        "directory"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- pack_remote_repository: Fetch, clone and package a GitHub repository into a consolidated file for AI analysis
    Input Schema:
		{
      "type": "object",
      "properties": {
        "remote": {
          "type": "string",
          "description": "GitHub repository URL or user/repo (e.g., yamadashy/repomix)"
        },
        "compress": {
          "type": "boolean",
          "default": true,
          "description": "Utilize Tree-sitter to intelligently extract essential code signatures and structure while removing implementation details, significantly reducing token usage (default: true)"
        },
        "includePatterns": {
          "type": "string",
          "description": "Specify which files to include using fast-glob compatible patterns (e.g., \"**/*.js,src/**\"). Only files matching these patterns will be processed. It is recommended to pack only necessary files."
        },
        "ignorePatterns": {
          "type": "string",
          "description": "Specify additional files to exclude using fast-glob compatible patterns (e.g., \"test/**,*.spec.js\"). These patterns complement .gitignore and default ignores. It is recommended to pack only necessary files."
        },
        "topFilesLength": {
          "type": "number",
          "default": 10,
          "description": "Number of top files to display in the metrics (default: 10)"
        }
      },
      "required": [
        "remote"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- read_repomix_output: Read the contents of a Repomix output file in environments where direct file access is not possible. This tool is specifically intended for cases where the client cannot access the file system directly, such as in web-based environments or sandboxed applications. For systems with direct file access, use standard file operations instead.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "outputId": {
          "type": "string",
          "description": "ID of the Repomix output file to read"
        }
      },
      "required": [
        "outputId"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- file_system_read_file: Read a file using an absolute path with security validation.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "path": {
          "type": "string",
          "description": "Absolute path to the file to read"
        }
      },
      "required": [
        "path"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

- file_system_read_directory: List contents of a directory using an absolute path.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "path": {
          "type": "string",
          "description": "Absolute path to the directory to list"
        }
      },
      "required": [
        "path"
      ],
      "additionalProperties": false,
      "$schema": "http://json-schema.org/draft-07/schema#"
    }

## tavily (`npx -y tavily-mcp@latest`)

### Available Tools
- tavily-search: A powerful web search tool that provides comprehensive, real-time results using Tavily's AI search engine. Returns relevant web content with customizable parameters for result count, content type, and domain filtering. Ideal for gathering current information, news, and detailed web content analysis.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "query": {
          "type": "string",
          "description": "Search query"
        },
        "search_depth": {
          "type": "string",
          "enum": [
            "basic",
            "advanced"
          ],
          "description": "The depth of the search. It can be 'basic' or 'advanced'",
          "default": "basic"
        },
        "topic": {
          "type": "string",
          "enum": [
            "general",
            "news"
          ],
          "description": "The category of the search. This will determine which of our agents will be used for the search",
          "default": "general"
        },
        "days": {
          "type": "number",
          "description": "The number of days back from the current date to include in the search results. This specifies the time frame of data to be retrieved. Please note that this feature is only available when using the 'news' search topic",
          "default": 3
        },
        "time_range": {
          "type": "string",
          "description": "The time range back from the current date to include in the search results. This feature is available for both 'general' and 'news' search topics",
          "enum": [
            "day",
            "week",
            "month",
            "year",
            "d",
            "w",
            "m",
            "y"
          ]
        },
        "max_results": {
          "type": "number",
          "description": "The maximum number of search results to return",
          "default": 10,
          "minimum": 5,
          "maximum": 20
        },
        "include_images": {
          "type": "boolean",
          "description": "Include a list of query-related images in the response",
          "default": false
        },
        "include_image_descriptions": {
          "type": "boolean",
          "description": "Include a list of query-related images and their descriptions in the response",
          "default": false
        },
        "include_raw_content": {
          "type": "boolean",
          "description": "Include the cleaned and parsed HTML content of each search result",
          "default": false
        },
        "include_domains": {
          "type": "array",
          "items": {
            "type": "string"
          },
          "description": "A list of domains to specifically include in the search results, if the user asks to search on specific sites set this to the domain of the site",
          "default": []
        },
        "exclude_domains": {
          "type": "array",
          "items": {
            "type": "string"
          },
          "description": "List of domains to specifically exclude, if the user asks to exclude a domain set this to the domain of the site",
          "default": []
        }
      },
      "required": [
        "query"
      ]
    }

- tavily-extract: A powerful web content extraction tool that retrieves and processes raw content from specified URLs, ideal for data collection, content analysis, and research tasks.
    Input Schema:
		{
      "type": "object",
      "properties": {
        "urls": {
          "type": "array",
          "items": {
            "type": "string"
          },
          "description": "List of URLs to extract content from"
        },
        "extract_depth": {
          "type": "string",
          "enum": [
            "basic",
            "advanced"
          ],
          "description": "Depth of extraction - 'basic' or 'advanced', if usrls are linkedin use 'advanced' or if explicitly told to use advanced",
          "default": "basic"
        },
        "include_images": {
          "type": "boolean",
          "description": "Include a list of images extracted from the urls in the response",
          "default": false
        }
      },
      "required": [
        "urls"
      ]
    }
