# 💻 Code Mode

## Specification 

- **ID**: `code`
- **Description**: The mode responsible for writing and editing code. It uses tools and APIs to perform code-related tasks.
- **System Prompt**: in the `.roo/system-prompt-code` file.
- **Available Tools**:
    - `read_file`
    - `search_files`
    - `list_files`
    - `list_code_definition_names`
    - `apply_diff`
    - `write_to_file`
    - `insert_content`
    - `search_and_replace`
    - `execute_command`
    - `use_mcp_tool`
    - `access_mcp_resource`
    - `ask_followup_question`
    - `attempt_completion`
    - `new_task` (only debug mode)
- **Available MCP Servers**:
    - `context7`
    - `repomix`
    - `tavily`
- **Can Spawn Tasks for Modes:**
    - debug

## Specifics of Thinking

The Code mode's `<thinking>` block focuses on:
*   Analyzing the incoming task message: Extracting the URID, checking for context file references (`@.roo/tasks/URID/...`) and the list of files.
*   Reading essential context files listed in the message using `read_file` *before* proceeding with coding.
*   Understanding the specific coding task requirements received from the Orchestrator, using the message and loaded context.
*   Analyzing existing code (`read_file`, `list_code_definition_names`) to determine where and how to make changes.
*   Planning the sequence of code modifications (e.g., add function, modify class, update imports).
*   Identifying potential side effects or necessary related changes in other parts of the code (`search_files`).
*   Choosing the appropriate tool for modification (`apply_diff`, `write_to_file`, `insert_content`, `search_and_replace`).
*   Planning verification steps, if applicable (`execute_command` for linting or simple checks).

## Response Format

The Code mode follows the standard response format:
```
<thinking>
Detailed code analysis and reasoning...
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
