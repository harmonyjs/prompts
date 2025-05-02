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

## Detailed Workflow Pattern

The Task mode has a more rigid algorithm compared to the Orchestrator:

1.  **Mandatory First Steps**:
    *   Generate a unique User Request ID (URID) and store it. Command: `echo "$(date +%Y%m%d-%H-%M)-$(uuidgen | tr '[:upper:]' '[:lower:]' | cut -c1-8)"`
    *   Create the dedicated task directory: `mkdir -p .roo/tasks/$(URID)` (replace `$(URID)` with the generated ID).
    *   Gather system information via `execute_command` (`pwd && tree --gitignore && npx -y envinfo --markdown && npm run`). Consider saving this output to a file within the task directory (e.g., `.roo/tasks/URID/initial_system_info.md`).
2.  **User Request Analysis**: Analyzing the user's request, identifying potential ambiguities or missing information.
3.  **Context Enrichment**: Potentially using tools like `read_file`, `list_files`, `search_files` to gather immediate context if deemed necessary to clarify the request before delegation. Save any large context retrieved into the `.roo/tasks/URID/` directory.
4.  **Delegation**: Formulating a high-quality, detailed prompt for the Orchestrator mode, including the URID and references to any context files saved in the task directory. Delegate the task using the `new_task` tool.

## Specifics of Thinking

The Task mode's `<thinking>` block focuses on:
*   Executing the mandatory first steps: generating URID, creating the task directory, gathering system info.
*   Analyzing the user's initial request for clarity, completeness, and feasibility.
*   Identifying missing information or context needed to formulate a robust task for the Orchestrator.
*   Planning the steps to gather necessary initial context (e.g., which files to read, what to search for) and deciding whether to save large context to the `.roo/tasks/URID/` directory.
*   Structuring the final, enriched prompt to be passed to the Orchestrator via `new_task`, ensuring it includes the URID and context file references.
*   Ensuring the mandatory first steps (`execute_command` for URID, directory creation, system info) are planned and executed.
