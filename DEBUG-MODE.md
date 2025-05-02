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
