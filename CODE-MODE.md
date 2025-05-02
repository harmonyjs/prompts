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
