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
```

## Detailed Workflow Pattern

The Task mode has a more rigid algorithm compared to the Orchestrator:

1.  **Mandatory First Step**: Gathering system information via `execute_command` (`pwd && tree --gitignore && npx -y envinfo --markdown && npm run`).
2.  **User Request Analysis**: Analyzing the user's request, identifying potential ambiguities or missing information.
3.  **Context Enrichment**: Potentially using tools like `read_file`, `list_files`, `search_files` to gather immediate context if deemed necessary to clarify the request before delegation.
4.  **Delegation**: Formulating a high-quality, detailed prompt for the Orchestrator mode and delegating the task using the `new_task` tool.

## Specifics of Thinking

The Task mode's `<thinking>` block focuses on:
*   Analyzing the user's initial request for clarity, completeness, and feasibility.
*   Identifying missing information or context needed to formulate a robust task for the Orchestrator.
*   Planning the steps to gather necessary initial context (e.g., which files to read, what to search for).
*   Structuring the final, enriched prompt to be passed to the Orchestrator via `new_task`.
*   Ensuring the mandatory first step (`execute_command`) is planned and executed.
