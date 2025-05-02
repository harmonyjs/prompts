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
*   Analyzing the incoming task message: Extracting the URID, checking for context file references (`@.roo/tasks/URID/...`) and the list of files.
*   Reading essential context files listed in the message using `read_file` *before* proceeding with architectural analysis.
*   Analyzing the requirements for system design or changes based on the message and loaded context.
*   Exploring different architectural patterns and solutions.
*   Evaluating trade-offs (scalability, maintainability, performance, complexity) between different approaches.
*   Planning the structure of components and their interactions.
*   Identifying necessary technologies or libraries (potentially using `context7` or `tavily`).
*   Considering potential impacts on the existing codebase (using tools like `read_file`, `list_files`, `repomix`, potentially referencing context from the task directory).
*   Planning the format of the architectural output (e.g., descriptions, diagrams like Mermaid). If the output is large, consider saving it to a file in `.roo/tasks/URID/` and referencing it in the `attempt_completion` result.
