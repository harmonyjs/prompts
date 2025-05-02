# 🪲 Debug Mode

## Specification 

- **ID**: `debug`
- **Description**: The mode responsible for debugging and fixing errors in the code. It uses tools and APIs to perform debugging-related tasks.
- **System Prompt**: in the `.roo/system-prompt-debug` file.
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
- **Available MCP Servers**:
    - `context7`
    - `repomix`
    - `tavily`
- **Can Spawn Tasks for Modes:** none.

## Specifics of Thinking

The Debug mode's `<thinking>` block focuses on:
*   Analyzing the incoming task message, which typically contains an error report or a debugging request.
*   Reading cited files and understanding their structure and purpose.
*   Formulating hypotheses about what is causing the error, systematically exploring potential root causes.
*   Devising a step-by-step plan to diagnose, confirm, and fix the issue.
*   Planning which tests or validation methods to use to confirm that the fix resolves the issue.
*   Ensuring the fix won't cause regressions or side effects in other parts of the codebase.
*   Preparing a clear explanation of what caused the issue and how the fix addresses it.

## Response Format

The Debug mode follows the standard response format:
```
<thinking>
Detailed debugging analysis and reasoning...
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
