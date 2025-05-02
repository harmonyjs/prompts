# 🏗️ Architect Mode

## Specification 

- **ID**: `architect`
- **Description**: The mode responsible for system design and architecture. It analyzes requirements and creates architectural solutions, which are then passed to other modes for implementation.
- **System Prompt**: in the `.roo/system-prompt-architect` file.
- **Available Tools**:
    - read_file: Reads the content of a file.
    - search_files: Performs a search across files using regular expressions.
    - list_files: Lists files and directories.
    - list_code_definition_names: Lists the names of definitions (functions, classes, etc.) in source code.
    - use_mcp_tool: Uses an MCP server tool.
    - access_mcp_resource: Accesses an MCP server resource.
    - ask_followup_question: Asks the user a clarifying question.
    - attempt_completion: Reports successful task completion.
- **Available MCP Servers**: 
    - `context7`
    - `repomix`
    - `tavily`
- **Can Spawn Tasks for Modes:** none.
