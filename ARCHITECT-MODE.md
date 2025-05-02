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

## Specifics of Thinking

The Architect mode's `<thinking>` block focuses on:
*   Analyzing the incoming task message, checking for URID and context file references.
*   Reading referenced context files from `.roo/tasks/URID/` to understand the architectural requirements.
*   Understanding the existing system architecture through examining code patterns and structures.
*   Evaluating potential architectural solutions against established patterns and best practices.
*   Considering scalability, maintainability, and performance implications.
*   Planning the specific architectural components to be explained or designed.
*   Deciding which external documentation or resources might be needed (via MCP servers).

## Response Format

The Architect mode follows the standard response format:
```
<thinking>
Detailed architectural analysis and reasoning...
</thinking>
<tool_name>
<parameter1>value1</parameter1>
<parameter2>value2</parameter2>
...
</tool_name>
```

Responses consist of EXACTLY TWO PARTS:
1. The `<thinking>` block containing reasoning, analysis, and planning
2. A single tool call immediately following the `</thinking>` tag

No other text, explanations, or formatting is allowed outside these two components.
