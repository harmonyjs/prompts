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
*   Analyzing the incoming task message: Extracting the URID, checking for context file references (`@.roo/tasks/URID/...`) and the list of files (which might contain error logs, stack traces, or relevant code snippets).
*   Reading essential context files listed in the message using `read_file` *before* proceeding with debugging.
*   Analyzing the error message, stack trace, and context provided in the task message and loaded context files.
*   Forming hypotheses about the root cause of the error.
*   Planning steps to verify hypotheses (e.g., reading specific code sections (`read_file`), checking related files, searching for similar issues (`tavily`)).
*   Pinpointing the exact location and cause of the bug.
*   Planning the code fix (`apply_diff`, `insert_content`, etc.).
*   Planning verification steps to ensure the fix works and doesn't introduce regressions (`execute_command` to run tests or the failing command).
