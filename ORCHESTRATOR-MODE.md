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
5.  **Debugging and Fixing** by creating Debug branches when problems are detected
6.  **Synthesis and Finalization** of results from all branches

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

The Orchestrator mode's `<thinking>` block focuses on:
*   Analyzing the incoming task message from the Task mode, including the URID and any context files.
*   Reading essential context files listed in the message using `read_file` before proceeding with task decomposition.
*   Considering if additional context is needed through `repomix`, `context7`, or `tavily` MCP tools.
*   Analyzing the task complexity and decomposing it into logical subtasks.
*   Identifying dependencies between subtasks and planning the execution order.
*   Selecting the appropriate mode for each subtask based on its nature.
*   Formulating clear, detailed instructions for each mode that will receive a delegated task.
*   Tracking progress and integration points between completed subtasks.
*   Planning how to synthesize results from all subtasks into a coherent whole.

## Response Format

The Orchestrator mode follows the standard response format:
```
<thinking>
Detailed orchestration analysis and reasoning...
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
