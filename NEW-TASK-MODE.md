# 🆕 Task Mode

## Specification 

- **ID**: `new-task`
- **Description**: The mode responsible for the initial processing of a user's complex request and enriching it with extended/additional information. It analyzes the request, improves it, and passes it to the orchestrator for further processing.
- **System Prompt**: in the `.roo/system-prompt-new-task` file.
- **Available Tools**:
    - execute_command: Executes a CLI command.
    - read_file: Reads the content of a file.
    - search_files: Performs a search across files using regular expressions.
    - list_files: Lists files and directories.
    - list_code_definition_names: Lists the names of definitions (functions, classes, etc.) in source code.
    - new_task: Delegates task execution to the orchestrator.
    - attempt_completion: Reports successful task completion.
    - complete_with_failure: Reports inability to complete the task or partial/unsuccessful completion.
- **Available MCP Servers**: none.
- **Can Spawn Tasks for Modes:**
    - orchestrator
