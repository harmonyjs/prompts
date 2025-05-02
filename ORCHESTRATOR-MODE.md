# 🪃 Orchestrator Mode

## Specification 

- **ID**: `orchestrator`
- **Description**: The mode responsible for coordinating task execution among different mode-agents. It receives tasks from the Task mode and delegates them to other modes based on their specialization.
- **System Prompt**: in the `.roo/system-prompt-orchestrator` file.
- **Available Tools**:
    - read_file: Reads the content of a file.
    - search_files: Performs a search across files using regular expressions.
    - list_files: Lists files and directories.
    - use_mcp_tool: Uses an MCP server tool.
    - ask_followup_question: Asks the user a clarifying question.
    - new_task: Delegates task execution to another mode-agent.
    - attempt_completion: Reports successful task completion.
- **Available MCP Servers**:
    - `context7`
    - `repomix`
    - `tavily`
- **Can Spawn Tasks for Modes:**
    - architect
    - code
    - debug
    - test
```

## Detailed Workflow Pattern

The Orchestrator's workflow represents the most complex process in the system:

1.  **Context and Knowledge Gathering**:
    *   Analysis of the initial user request
    *   Study of the codebase structure via `list_files` and `read_file`
    *   Collection of documentation for mentioned technologies via `context7`
    *   If necessary, codebase analysis via `repomix`
    *   Search for relevant information on the internet via `tavily`
2.  **Task Decomposition** into logical subtasks with identification of their dependencies
3.  **Architecture Delegation** to the Architect mode with transfer of gathered context
4.  **Solution Implementation** through sequential creation of Code mode branches
5.  **Testing** through alternation with Test mode branches
6.  **Debugging and Fixing** by creating Debug branches when problems are detected
7.  **Synthesis and Finalization** of results from all branches

## MCP Usage Strategy Details

The Orchestrator's system prompt contains a special `MCP USAGE STRATEGY` section that details when and how to use various MCP servers:

```
Overarching Principle: Proactive Context Gathering
If fetching external context could reasonably improve the quality, accuracy, or safety of your plan or the instructions you delegate, you should prioritize doing so.
```

This strategy details a **decision tree** for using MCP servers:

1.  **Check for Explicit Technology Mentions (`context7`)**:
    *   **Trigger**: Mention of specific libraries, frameworks, APIs, SDKs
    *   **Action**: Mandatory call to `context7` to get documentation
2.  **Assess Need for Codebase Overview (`repomix`)**:
    *   **Trigger**: Complex changes to internal logic, potential impact on multiple files
    *   **Action**: Recommended use of `repomix` to analyze codebase structure
3.  **Consider Need for External Web Context (`tavily`)**:
    *   **Trigger**: Need for information beyond official documentation and the local codebase
    *   **Action**: Use `tavily` for web searches

## Specifics of Thinking

The Orchestrator's `<thinking>` block concentrates on:
*   Analyzing the incoming task message: Extracting the URID, checking for context file references (`@.roo/tasks/URID/...`) and the list of files.
*   Reading essential context files listed in the message using `read_file` *before* proceeding with planning.
*   Task decomposition into subtasks.
*   Choosing a delegation strategy (which mode for which subtask).
*   Determining the need for *additional* context gathering (using MCP servers like `context7`, `repomix`, `tavily`, or reading more files). If large context is gathered, plan to save it to the `.roo/tasks/URID/` directory using `write_to_file`.
*   Selecting the appropriate mode for each subtask.
*   Formulating the `message` for `new_task`, ensuring it includes the URID, relevant context (potentially referencing newly saved files in the task directory), the invocation tree, and the `@.roo/tasks/URID/` path marker.
*   Analyzing the results (`<result>` tag content) of completed subtasks to decide the next step (proceed, re-delegate, adapt plan, ask user).
*   Synthesizing results from different branches.
*   Evaluating overall task completion against the original request.

### Example of Structured Thinking

```
<thinking>
Analyzing the task of creating a user authentication API.

1. Delegated subtasks and their status:
   - Architect (design API structure): Completed ✅
   - Code (implement login/registration routes): Completed ✅
   - Code (implement protected routes): Completed ✅
   - Test (test all routes): Completed ✅

2. Synthesizing results:
   - Architect designed the architecture using JWT, defined data structures and routes.
   - Code implemented registration, login, validation, token issuance, and verification.
   - Test confirmed the functionality of all routes and error handling.

3. Compliance with the original task:
   - ✅ Implemented authentication with login and password
   - ✅ Implemented JWT token issuance
   - ✅ Implemented token verification for protected routes
   - ✅ Added error handling and data validation

4. Conclusion: All aspects of the original task are satisfied. I can call attempt_completion.
</thinking>
```

## Error Handling Decision Tree

The Orchestrator employs a sophisticated error handling process when dealing with subtask results or tool errors:

```mermaid
graph TD
    A[Received subtask result/error] --> B{Analyze result};
    B -- Successful & meets requirements? --> C[Proceed to next subtask];
    B -- Unsuccessful / Doesn't meet --> D{Error correctable? / Attempts < 3?};
    D -- Yes (e.g., parameter typo, retry subtask) --> E[Retry with corrections / Re-delegate];
    D -- No --> F{Re-delegation possible with modified instructions?};
    F -- Yes (e.g., incomplete result needs refinement) --> G[Modify instructions & re-delegate];
    F -- No --> H{Plan adaptation possible?};
    H -- Yes --> I[Adapt plan & continue];
    H -- No --> J{Ask user?};
    J -- Yes --> K[Use ask_followup_question];
    J -- No --> L[Acknowledge Failure (Fail - potentially via parent)];

    E --> B; // Re-analyze after retry/re-delegation
    G --> B; // Re-analyze after re-delegation
    I --> C; // Continue after plan adaptation
    K --> B; // Re-analyze after getting user input

    style L fill:#fbb,stroke:#333,stroke-width:2px
    style C fill:#bfb,stroke:#333,stroke-width:2px
```
