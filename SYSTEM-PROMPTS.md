# System Prompts in Roo

This document details the structure, formation, and best practices for creating system prompts used by the Roo multi-agent system. System prompts are crucial instructions that define the behavior, capabilities, and interaction patterns of each specialized mode-agent within Roo.

## 1. Introduction to System Prompts

- **Definition**: A System Prompt is the core instruction set provided to the underlying Language Model (LLM) to configure it as a specific Roo mode-agent. It dictates the agent's role, available tools, interaction rules, delegation capabilities, and overall objective within the software development workflow.
- **Location**: Each mode-agent's system prompt is stored in a dedicated file within the `.roo/` directory, following the pattern `.roo/system-prompt-<mode-id>` (e.g., `.roo/system-prompt-orchestrator`).
- **Relationship to Base Prompts**: System prompts are composed by combining and specializing foundational instructions stored in the `.roo/base-prompts/` directory (e.g., tool definitions, general rules, objective templates). This modular approach promotes consistency and maintainability.

## 2. System Prompt Structure

System prompts are carefully structured to provide clear and unambiguous guidance to the LLM. While the exact structure varies slightly depending on the mode's specific role, they generally follow common principles and include standardized sections.

### General Principles of System Prompt Structuring

1.  **Role Orientation**: Each prompt begins with a clear definition of the mode-agent's role and primary responsibilities.
2.  **Standardized Sections**: Most prompts contain standard sections like `TOOL USE`, `CAPABILITIES`, `RULES`, and `OBJECTIVE`.
3.  **Specialized Sections**: Depending on the mode's specifics, specialized sections may be added (e.g., `MCP USAGE STRATEGY` for Orchestrator, detailed step-by-step processes for Task).
4.  **Process Orientation**: Each prompt describes the mode's typical workflow or decision-making process, though the level of detail varies.
5.  **Tool Availability**: Clearly defines which tools (including MCP tools) are available to the mode and the precise format for invoking them.
6.  **Hierarchical Binding**: Specifies which other modes the current mode can delegate tasks to using the `new_task` tool.

### Example Structures

Let's examine the structure using the Orchestrator and New Task modes as examples:

#### Orchestrator (`.roo/system-prompt-orchestrator`)

The Orchestrator prompt emphasizes task decomposition, delegation, and context management.

```
# 🪃 Orchestrator Mode

As an orchestrator, you should:

1.  When given a complex task, break it down into logical subtasks...
2.  For each subtask, use the `new_task` tool to delegate...
3.  Track and manage progress...
...

## TOOL USE
Description of the tool usage format (strict XML) and a detailed list of available tools: `read_file`, `search_files`, `list_files`, `use_mcp_tool`, `ask_followup_question`, `attempt_completion`, `new_task`.

## MCP USAGE STRATEGY
A specific section describing the strategy for using MCP servers (like context7, repomix, tavily) to gather context *before* task decomposition. Defines proactive context gathering principles.

## MCP SERVERS
Description of connected MCP servers and their available tools.

## CAPABILITIES
List of the mode's capabilities (e.g., file system access, command execution via tools, MCP interaction).

## MODES
Description of available modes to which the Orchestrator can delegate tasks (`architect`, `code`, `debug`, `test`).

## RULES
Detailed rules and limitations for the Orchestrator mode, covering general behavior, filesystem operations, task delegation, user interaction, and error handling.

## OBJECTIVE
Final summary of the goal (coordinate task execution) and working methodology (decompose, delegate, synthesize).
```

#### New Task (`.roo/system-prompt-new-task`)

The New Task prompt focuses on understanding the user request, gathering initial context, and preparing the task for the Orchestrator.

```
# 🆕 New Task Mode

## YOUR GOAL
Detailed description of the mode's main goal: analyze the user's request, gather essential system/project context, formulate a high-quality, well-defined task prompt for the Orchestrator, and delegate the task using `new_task`.

## Step-by-step process
Detailed description of the workflow:
1.  MANDATORY First Step: Gather system information (`execute_command pwd && tree ...`).
2.  Analyze user request and gathered context.
3.  Identify ambiguities; potentially use `ask_followup_question`.
4.  Formulate the detailed task message for the Orchestrator.
5.  Delegate using `new_task` with `mode: orchestrator`.
6.  Report delegation success/failure using `attempt_completion` or `complete_with_failure`.

## TOOL USING
Detailed description of available tools (`execute_command`, `ask_followup_question`, `new_task`, `attempt_completion`, `complete_with_failure`) with emphasis on their use in specific steps of the process. Includes retry logic for `execute_command`.

## OBJECTIVE
Brief summary of the mode's goal (prepare and delegate task) and working methodology (context gathering, formulation, delegation).

## SYSTEM INFORMATION
Placeholder for basic system information, highlighting the need to gather more via the mandatory first step.
```

## 3. System Prompt Formation

Creating an effective system prompt involves several steps, leveraging the base prompts and adding mode-specific logic:

1.  **Composition from Base Prompts**: Start by combining relevant foundational components from `.roo/base-prompts/` (e.g., `tools.md`, `objective.md`, `general-rules.md`, `mcp-servers.md`). These provide the common language and definitions.
2.  **Role Specialization**: Add specific instructions, responsibilities, and workflow descriptions tailored to the mode's unique role (e.g., the detailed step-by-step process for New Task, the decomposition/delegation logic for Orchestrator).
3.  **Defining Tool Availability**: Explicitly list the tools (standard and MCP) available to *this specific mode*. Remove definitions for tools the mode should not access.
4.  **Setting Delegation Rules**: Clearly define which other modes can be invoked using the `new_task` tool within the `MODES` or `RULES` section.
5.  **Establishing Rules and Limitations**: Add mode-specific rules, constraints, and behavioral guidelines (e.g., mandatory first steps, specific error handling logic, interaction protocols).

This process ensures that each system prompt is both consistent with the overall system design (through base prompts) and precisely tailored to the specific function of the mode-agent.

### Visualization of System Prompt Formation

```mermaid
graph TD
    subgraph ".roo/base-prompts/"
        A[tools.md]
        B[objective.md]
        C[system-information.md]
        D[rules-general.md]
        E[mcp-servers.md]
        F[...]
    end

    subgraph "Specialization for Mode (e.g., Orchestrator)"
        G{Composition & Adaptation}
        H[Role & Responsibilities Definition]
        I[Mode-Specific Workflow/Strategy (e.g., MCP Usage)]
        J[Tool Filtering & Availability]
        K[Delegation Rules (Allowed Modes)]
        L[Mode-Specific Rules & Constraints]
    end

    subgraph ".roo/system-prompt-orchestrator"
        M[Final System Prompt for Orchestrator]
    end

    A --> G;
    B --> G;
    C --> G;
    D --> G;
    E --> G;
    F --> G;

    G --> M;
    H --> M;
    I --> M;
    J --> M;
    K --> M;
    L --> M;

    style G fill:#f9f,stroke:#333,stroke-width:2px
    style M fill:#ccf,stroke:#333,stroke-width:2px
```

## 4. Best Practices for Creating System Prompts

The effectiveness of Roo hinges on high-quality system prompts. Follow these best practices:

### Structuring System Prompts

Ensure prompts contain:

1.  **Header**: `# [Emoji] [Mode Name] Mode` (e.g., `# 🪃 Orchestrator Mode`).
2.  **Role/Goal**: Clear statement of the mode's purpose and primary functions.
3.  **TOOL USE**: Precise definition of the XML format and detailed descriptions of *all available* tools for that mode.
4.  **Workflow/Process**: Description of the typical steps or decision-making logic.
5.  **Mode-Specific Sections**: As needed (e.g., `MCP USAGE STRATEGY`).
6.  **RULES**: Comprehensive rules covering behavior, tool use, delegation, interaction, and error handling. Use sub-sections for clarity.
7.  **OBJECTIVE**: Concise summary of the goal.
8.  **Examples**: Crucial for illustrating correct formats (tool calls, `new_task` messages, invocation trees) and expected behavior.

### Critical Elements Requiring Special Attention

Pay close attention to:

1.  **`new_task` Message Structure**: Mandate the exact format (emoji, short desc, URID, details, context, invocation tree, context file list, next step marker with `@` path). Include a clear, complete example incorporating the URID and context file references.
2.  **`invocation tree` Concept**: Detail its purpose, formation rules, and the `^ we are here` marker. Provide an example.
3.  **Context Sharing Mechanism**: Clearly explain the `.roo/tasks/URID/` directory usage for large context. Detail the responsibilities of `new-task` (generation, creation) and other modes (saving, referencing, consuming).
4.  **Self-Analysis/Result Evaluation**: Define criteria for checking task completion *before* calling `attempt_completion`. Include checklists if helpful.
5.  **Error Handling & Fault Tolerance**: Specify retry limits (max 3 total attempts), progressive strategies (correct, retry, adapt, fail), and criteria for failure (`complete_with_failure`) or asking the user (`ask_followup_question`). Include handling for context file errors (e.g., file not found).
6.  **Output Formatting**: Strictly enforce the response structure (e.g., `<thinking>` block followed *immediately* by one tool call). Explicitly forbid extra text or markdown. Provide clear examples of correct/incorrect output.

### Checklist for Verifying System Prompts

- [ ] **Clarity & Unambiguity**: Is the language precise? Could any instruction be misinterpreted?
- [ ] **Completeness**: Are all tools, rules, processes, context sharing, and edge cases covered?
- [ ] **Consistency**: Is the prompt internally consistent? Does it align with the overall system architecture (`README.md`)?
- [ ] **Accuracy**: Does it correctly represent the mode's intended capabilities and limitations?
- [ ] **Structure & Readability**: Is it well-organized with clear headings, lists, and formatting?
- [ ] **Tool Definitions**: Are all *available* tools listed and accurately described? Are *unavailable* tools omitted?
- [ ] **Delegation Rules**: Are the allowed modes for `new_task` clearly specified?
- [ ] **`new_task` Message Format**: Is the required structure (including URID, context files, `@` path) clearly defined and exemplified?
- [ ] **Context Consumption**: Does the prompt instruct the mode to check for and read context files referenced in the `new_task` message?
- [ ] **Examples**: Are there clear examples for critical formats and concepts?
- [ ] **Error Handling**: Is the strategy clear, robust, and consistent (including context file errors)?
- [ ] **Formatting Rules**: Are the strict output formatting requirements explicit?
- [ ] **LLM Address**: Is it written as direct instructions ("You must...", "Your task is...")?

### Typical Problems and Solutions

1.  **Ambiguity/Vagueness**: Instructions are unclear.
    *   **Solution**: Rephrase using precise language. Use examples. Break down complex instructions.
2.  **Inconsistent Formatting/Structure**: Prompts differ significantly in organization.
    *   **Solution**: Adopt a standard template (like the sections listed above) and adapt it for each mode. Use consistent Markdown.
3.  **Missing `new_task` Structure/Example**: Delegation messages are malformed.
    *   **Solution**: *Always* include the full, detailed structure definition and a concrete example in *every* prompt that uses `new_task`.
4.  **Weak Error Handling**: Agent gets stuck or fails inappropriately.
    *   **Solution**: Implement the standard 3-attempt retry logic. Define clear criteria for failure vs. asking for help vs. adapting the plan.
5.  **Incorrect Output Format**: Agent adds extra text or uses wrong tags.
    *   **Solution**: Be extremely explicit about the required output format. Use negative constraints ("NEVER add text after the final tool call"). Provide clear examples of *correct* and *incorrect* output.
6.  **Lack of Self-Analysis**: Agent completes tasks prematurely or incorrectly.
    *   **Solution**: Add explicit instructions and checklists for self-verification before `attempt_completion`.
7.  **Missing Context Handling**: Agent ignores context files or fails if they are missing.
    *   **Solution**: Add explicit instructions in system prompts for *all* receiving modes to check the `new_task` message for URID, context file lists, and the `@` path. Instruct them to use `read_file` for listed files *before* starting work. Add error handling for `read_file` failures related to context files.

## 5. Error Handling Mechanisms and Fault Tolerance in Prompts

Robust error handling is defined *within* the system prompts to guide the LLM's behavior when issues arise.

### Types of Handled Errors (Defined in Prompts)

Prompts should instruct the agent on how to recognize and potentially handle:

-   **Tool Call Errors**: Invalid parameters (paths, JSON), access issues, network errors (MCP), system errors reported by Roo.
-   **Unsatisfactory Subtask Results**: When a delegated task completes (`attempt_completion` received), but the `<result>` content doesn't meet the requirements set by the delegating mode (primarily handled by Orchestrator).
-   **Information Gathering Failures**: `read_file` on non-existent file (including context files from `.roo/tasks/URID/`), `search_files` yields no results, MCP tool errors.
-   **Fundamental Task Unfeasibility**: Identified during initial analysis (e.g., user request is contradictory or impossible).

### Fault Tolerance Strategies (Defined in Prompts)

Prompts explicitly define these strategies:

#### Retry Logic

-   **Instruction**: Prompts (especially for tools like `execute_command`, `read_file`, `use_mcp_tool`) explicitly state a retry limit, typically **max 3 total attempts (1 initial + 2 retries)**.
-   **Guidance**: Instruct the agent to attempt self-correction (e.g., fix a typo in a path) before retrying.
-   **Example Prompt Snippet (Task Mode)**:
    ```
    If a previous tool call (for these tools) failed (due to parameter error or system issue from Roo), internally attempt to self-correct and decide on retrying up to two additional times (max 3 total attempts for that specific information gathering step).
    ```

#### Failure Handling

-   **Instruction**: Define criteria for when to give up after retries fail or when an error is clearly unrecoverable.
-   **Tools**: Specify which tool to use to signal failure (e.g., `complete_with_failure` for Task mode).
-   **Alternative Actions**: Instruct modes like Orchestrator to consider `ask_followup_question` or adapting the plan instead of immediate failure if appropriate.

#### Re-delegation (Orchestrator Specific)

-   **Instruction**: The Orchestrator's prompt details how to analyze the `<result>` of a completed subtask and, if unsatisfactory, modify the instructions (`message` in `new_task`) and re-delegate the task (potentially to the same or a different mode).
-   **Example Prompt Snippet (Orchestrator)**:
    ```
    When a subtask completes... analyze the <result> content... Determine if the outcome meets requirements... decide the next step: delegate the next subtask... ask the user for clarification, or potentially re-delegate the same subtask with modified instructions if the previous attempt was insufficient.
    ```

#### Plan Adaptation (Orchestrator Specific)

-   **Instruction**: Guide the Orchestrator to adjust its overall task decomposition and delegation plan based on the success, failure, or results of completed subtasks. This leverages the iterative nature of its workflow.

### Criteria for Choosing Strategies (Defined in Prompts)

Prompts should guide the LLM on *when* to apply each strategy:

-   **Retry**: Error seems correctable (typo, temporary network issue) AND attempt count < 3.
-   **Adapt/Ask/Re-delegate (Orchestrator)**: Subtask result is unsatisfactory but potentially fixable with different instructions or more context.
-   **Fail**: Error is fundamental (file definitely missing), retries exhausted, necessary info unobtainable, initial analysis shows task is impossible.

By embedding these mechanisms directly into the system prompts, Roo ensures consistent and robust handling of common issues across its different modes.
