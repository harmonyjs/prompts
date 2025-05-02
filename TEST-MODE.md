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
