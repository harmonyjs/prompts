# 🔍 Test Mode

## Specification 

- **ID**: `test`
- **Description**: The mode responsible for testing and verifying code, particularly after changes in `code` and `debug` modes. It uses tools and APIs to perform testing-related tasks.
- **System Prompt**: in the `.roo/system-prompt-test` file.
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
    - `new_task` (only debug mode)
    - `attempt_completion`
- **Available MCP Servers**:
    - `context7`
    - `repomix`
    - `tavily`
- **Can Spawn Tasks for Modes:**
    - debug
```

## Specifics of Thinking

The Test mode's `<thinking>` block focuses on:
*   Understanding the functionality or code changes that need to be tested.
*   Identifying critical paths, edge cases, and potential failure points.
*   Planning test cases (unit, integration, etc.) to cover the requirements.
*   Determining the structure and location for test files (`write_to_file`).
*   Writing the test code, potentially reading existing tests or application code for context (`read_file`).
*   Planning the command to execute the tests (`execute_command`).
*   Analyzing test results to determine success or failure.
*   If tests fail due to application code bugs, planning the delegation to the Debug mode (`new_task`), including necessary context like error messages and relevant file paths.
