# Prompts

This document describes a multi-agent intelligent system integrated into a next-generation intelligent integrated development environment (IDE), built around the concept of deep integration of agentic LLM systems into the programming process. This agent system is capable of understanding user intentions, planning complex action chains, delegating tasks, and adapting to context.

## System Architecture

### Key Components

- **Roo**: A multi-agent system consisting of several specialized modes-agents, each responsible for specific aspects of software development.

- **Mode**: Specialized agents that perform various tasks during the development process. Each mode has unique capabilities and functions that allow it to perform its work effectively.

- **System Prompt**: The main instruction that defines the behavior and capabilities of each mode-agent in Roo. It includes information about available tools, functions, and ways of interacting with the user. **See [SYSTEM-PROMPTS.md](SYSTEM-PROMPTS.md) for a detailed explanation of their structure, formation, and best practices.**

- **Tools**: Functions and APIs that can be used by mode-agents to perform tasks. Tools can include functions for working with code, APIs for interacting with external systems, and other useful resources. Tools are called using a special XML-like syntax that allows mode-agents to interact with them.

- **MCP Servers**: Special servers that provide additional tools and resources for task execution. Each server can offer different capabilities that can be used by mode-agents for more efficient task completion.

- **LLM**: The language model that Roo instructs with a specific system prompt, assigning it a particular mode, making it a specialized agent capable of performing tasks within that mode. The model receives instructions and context from the system prompt and then uses its capabilities to perform tasks, delegating them to other mode-agents or using available tools.

- **User**: The end-user who interacts with Roo and assigns tasks for execution. The user can ask questions, give commands, and expect Roo to perform tasks.

- **Branch**: User task execution consists of several stages, each representing a separate branch initiated by the preceding mode-agent via the `new_task` tool. Each dedicated branch returns its result to the parent branch after confirming task completion via the `attempt_completion` tool. Each branch is a specific mode-agent with its own context window. Branching allows Roo to efficiently handle complex tasks by breaking them down into smaller subtasks that can be performed by different mode-agents.

### Key Process Flow

1. User enters a request (which can be in various forms: direct task, question, UI interaction).
2. The **Task** mode receives the initial request, gathers essential context (execute_command to gather system info, read_file for specific files, etc.) and delegates it to the **Orchestrator** mode.
3. The **Orchestrator** analyzes the task, breaks it down, and delegates various subtasks to specialized modes (Architect, Code, Debug, Test).
4. Each specialized mode performs its task, may delegate further, and reports back to its calling mode.
5. The **Orchestrator** synthesizes all results and the task culminates in the final result returned to the user.

### Agent (LLM) Response Format

All agent responses in the Roo system consist of EXACTLY TWO PARTS:

1. A `<thinking>` block containing the agent's reasoning, analysis, and planning
2. A single tool call immediately following the `</thinking>` tag

**CORRECT FORMAT:**
```
<thinking>
I need to examine the existing configuration file to understand the current settings
before I can propose changes.
</thinking>
<read_file><path>config.json</path></read_file>
```

**CORRECT FORMAT:**
```
<thinking>
I need to determine which database technology to use before I can finalize the architecture.
The user hasn't specified this critical information yet.
</thinking>
<ask_followup_question><question>Which DB to choose: PostgreSQL or MySQL?</question></ask_followup_question>
```

**Tool Format Reminder:** Always use the strict XML format: `<tool_name><parameter_name>value</parameter_name>...</tool_name>`.

**Incorrect Format (Avoid!):**
```
<!-- INCORRECT: Text outside thinking block -->
I'll read the configuration file.
<thinking>...</thinking>
<read_file><path>config.json</path></read_file>

<!-- INCORRECT: Text after the tool -->
<thinking>...</thinking>
<read_file><path>config.json</path></read_file>
File read.

<!-- INCORRECT: Tool wrapped in Markdown -->
<thinking>...</thinking>
```xml
<read_file><path>config.json</path></read_file>
```
```

## Mode Hierarchy

There are 6 modes in total:

- **🆕 Task**: See [NEW-TASK-MODE.md](NEW-TASK-MODE.md) for a detailed description.
    - **ID**: `new-task`
    - **Can Spawn Tasks for Modes:**
        - orchestrator

- **🪃 Orchestrator**: See [ORCHESTRATOR-MODE.md](ORCHESTRATOR-MODE.md) for a detailed description.
    - **ID**: `orchestrator`
    - **Can Spawn Tasks for Modes:**
        - architect
        - code
        - debug

- **🏗️ Architect**: See [ARCHITECT-MODE.md](ARCHITECT-MODE.md) for a detailed description.
    - **ID**: `architect`
    - **Can Spawn Tasks for Modes:** none.

- **💻 Code**: See [CODE-MODE.md](CODE-MODE.md) for a detailed description.
    - **ID**: `code`
    - **Can Spawn Tasks for Modes:**
        - debug

- **🪲 Debug**: See [DEBUG-MODE.md](DEBUG-MODE.md) for a detailed description.
    - **ID**: `debug`
    - **Can Spawn Tasks for Modes:** none.


Thus, the mode hierarchy looks as follows:

```
🆕 Task
  └── 🪃 Orchestrator
      ├── 🏗️ Architect
      ├── 💻 Code
      │   └── 🪲 Debug
      └── 🪲 Debug
```

🆕 Task can spawn a branch with the 🪃 Orchestrator mode. 🪃 Orchestrator can spawn branches with 🏗️ Architect, 💻 Code, and 🪲 Debug modes. 🏗️ Architect cannot spawn other branches. 💻 Code can only spawn 🪲 Debug branches. 🪲 Debug cannot spawn other branches.


## Branching with a Different Mode

When a mode-agent decides that it is necessary to branch off with another mode, it uses the `new_task` tool, which allows it to create a new task and pass it to another mode-agent. Each mode-agent has its own allowed modes with which it can interact. For example, the `💻 Code` mode-agent can only branch off to the `🪲 Debug` mode-agent, while the `🪃 Orchestrator` mode-agent can branch off to the `🏗️ Architect`, `💻 Code`, and `🪲 Debug` mode-agents.

### How Branching Occurs

1. The mode-agent analyzes the current task and determines that it needs to branch off with another mode.
2. It uses the `new_task` tool to create a new task and pass it to another mode-agent.
3. When creating the new task, the mode-agent specifies which mode-agent will perform this task from the available allowed modes using the `mode` argument.
4. A detailed description of the delegated task, including all necessary parameters and context, is passed as the `message`.
5. The new mode-agent receives the task and begins its execution, using its own tools and capabilities.
6. After completing the task, the new mode-agent reports task completion via the `attempt_completion` tool, which returns the result to the parent branch.
7. The parent branch receives the task execution result and continues its work based on the received information.

### Structure of `message` in `new_task`

The `message` in the `new_task` tool must be structured precisely as follows to ensure proper context transfer and task understanding:

```
[emoji defining the mode] [short task description, no longer than 5-8 words]

URID: [User Request ID generated by new-task mode]

[detailed task description including all necessary details, prerequisites, and requirements]

[additional context for task execution if necessary, potentially referencing files in the task directory]

[current invocation tree]

[Optional: List of relevant context files saved in the task directory]

[indication of the next step in the task execution tree, potentially including the context directory path]
```

**Key Components:**

1.  **Header Line:** Emoji + Short Description (e.g., `💻 Implement user profile update endpoint`).
2.  **URID:** The unique identifier for the overall user request (e.g., `URID: 20250502-11-36-b4649193`). This MUST be included.
3.  **Detailed Description:** The core instructions for the subtask.
4.  **Additional Context:** Any immediate context snippets.
5.  **Invocation Tree:** The standard hierarchy showing the call stack.
6.  **Context File List (Optional but Recommended):** If large context was saved to the task directory, list the relevant files here explicitly.
7.  **Next Step / Context Path:** Instructions on what to do next, potentially including the special context path marker.

**Context Directory Path Marker:**
If context files are saved and listed, the message should end with a line indicating the path to the task's context directory, prefixed with `@`:
`@.roo/tasks/[URID]/`

This marker signals to the receiving mode where to find the shared context files.

**Example:**

```
💻 Implement user profile update endpoint

URID: 20250502-11-36-b4649193

Create a new endpoint PUT /api/users/profile that accepts user profile data (name, email) and updates the authenticated user's record in the database. Use the existing database connection from `db.js` and validation logic from `validators.js`.

Context: Project uses Express.js, user ID is available in `req.user.id`. Relevant files: `routes/users.js`, `models/user.js`, `lib/db.js`, `lib/validators.js`.

🆕 Add user profile editing feature
└── 🪃 Orchestrate profile editing implementation
    └── 💻 Implement user profile update endpoint
        ^ we are here

Relevant context files:
.roo/tasks/20250502-11-36-b4649193/api-specification.md
.roo/tasks/20250502-11-36-b4649193/database-schema.sql

Ensure proper error handling for database errors and validation failures. Return the updated user profile on success. Read the context files first.
@.roo/tasks/20250502-11-36-b4649193/
```

### What is an `invocation tree`

The `invocation tree` is a structure that shows the hierarchy of mode-agent calls. It helps understand which mode-agents were called, in what order, and which mode-agent we are currently in. Each mode-agent in the invocation tree is represented by its emoji and a short description of the task it performs. The invocation tree helps maintain context and track task execution in complex scenarios.

Example of what an `invocation tree` might look like:

```
🆕 Refactor user authentication module
└── 🪃 Orchestrate the refactoring process
```

##### Example Workflow (Debug)

1.  **Receiving Task (from Code mode):**
    ```
    🪲 Fix TypeError in calculateTotal

    Running tests (`npm test`) results in a TypeError: Cannot read properties of undefined (reading 'price') in the `calculateTotal` function in `src/utils.js` line 25.

    Test Output Snippet:
    ```
    TypeError: Cannot read properties of undefined (reading 'price')
        at calculateTotal (/Users/andreyvavilov/Projects/meta-prompts/src/utils.js:25:30)
        at Object.<anonymous> (/Users/andreyvavilов/Projects/meta-prompts/test/utils.test.js:15:9)
        ...
    ```

    Relevant files: `src/utils.js`, `test/utils.test.js`.

    🆕 Refactor user authentication module
    └── 🪃 Orchestrate the refactoring process
        └── 💻 Implement password hashing update
            └── 🪲 Fix TypeError in calculateTotal
                ^ we are here

    Please identify the cause of the TypeError and apply a fix. Verify the fix by running `npm test`.
    ```

2.  **Analysis and Planning (`<thinking>`):**
    ```xml
    <thinking>
    Task: Fix TypeError in `calculateTotal` (`src/utils.js:25`). Error 'Cannot read properties of undefined (reading 'price')'.
    1. Hypothesis: The function receives an element without the `price` property, possibly `undefined` or an object of a different type. Likely due to incorrect test data or improper handling of the input array in the function itself.
    2. Plan:
        - Read `src/utils.js` lines 20-30 to see the `calculateTotal` code.
        - Read `test/utils.test.js` lines 10-20 to see what data is passed in the test.
        - Determine the cause: incorrect test data or lack of checking in the function.
        - Apply the fix: either correct the test or add a check in `calculateTotal` (e.g., `item && item.price`).
        - Run `npm test` to verify.
    3. Start by reading `src/utils.js`.
    </thinking>
    ```

3.  **Reading Files (Example Step):** Agent uses `<read_file><path>src/utils.js</path><start_line>20</start_line><end_line>30</end_line></read_file>`.
    *(Code analysis)*
    Then the agent uses `<read_file><path>test/utils.test.js</path><start_line>10</start_line><end_line>20</end_line></read_file>`.
    *(Test analysis, identifying the problem - e.g., passing `[ { price: 10 }, undefined ]`)*

4.  **Applying Fix (Example Step):** The agent decides to add a check in the function and uses `<apply_diff>` to modify line 25 in `src/utils.js`.

5.  **Verifying Fix (Example Step):** The agent uses `<execute_command><command>npm test</command></execute_command>`.
    *(Analysis of command output)*

6.  **Completion:** If tests pass, the agent uses `<attempt_completion>`, describing the identified cause and the applied fix.

```
│   ├── 🏗️ Design new authentication flow
│   ├── 💻 Implement JWT token generation
│   │   └── 🪲 Fix token expiration logic error
│   ├── 💻 Implement password hashing update
│   └── 💻 Complete entire authentication flow
│   │   ├── 🪲 Debug crash on startup
│   │   ├── 🪲 Debug failing login test case
│   │   ^ we are here
```

This tree shows that the initial refactoring task (🆕 Refactor...) was passed to the orchestrator (🪃 Orchestrate...). The orchestrator spawned tasks for the architect (🏗️ Design...), three tasks for the coder (💻 Implement...Complete...). One of the coding tasks required debugging (🪲 Fix...), and the last coding task also revealed two problems requiring debugging (🪲 Debug...), and we are currently in this last debug branch for the second problem.

## Context Sharing via Task Directories

To handle context larger than what comfortably fits in the `new_task` message (e.g., full file contents, large command outputs, complex analysis results), Roo utilizes a dedicated directory structure within `.roo/tasks/`.

**Workflow:**

1.  **URID Generation (`new-task`):** Upon receiving a user request, the `new-task` mode generates a unique User Request ID (URID), e.g., `20250502-12-15-a1b2c3d4`.
2.  **Directory Creation (`new-task`):** It immediately creates a corresponding directory: `.roo/tasks/URID/` (e.g., `.roo/tasks/20250502-12-15-a1b2c3d4/`).
3.  **Context Saving (Any Mode):** As modes gather information (e.g., `new-task` gathering system info, `orchestrator` reading files or getting MCP results), they can save large outputs as files within this directory (e.g., using `write_to_file` with a path like `.roo/tasks/URID/initial_context.md`).
4.  **Context Referencing (`new_task`):** When delegating a task, the sending mode includes:
    *   The `URID` in the `message`.
    *   A list of relevant context filenames within the task directory.
    *   The special `@.roo/tasks/URID/` path marker at the end of the `message`.
5.  **Context Consumption (Receiving Mode):** The mode receiving the task:
    *   Identifies the URID and the context path marker (`@...`).
    *   Reads the list of specified context files from the message.
    *   Uses `read_file` to load the content of these files from the `.roo/tasks/URID/` directory *before* starting the main task logic.

This mechanism allows for the transfer of rich, extensive context between modes without overloading the `message` parameter, ensuring that subsequent modes have all the necessary information to perform their tasks effectively.

## Individual Tool Usage Scenarios

Although all mode-agents have access to a certain set of tools, the nature and purpose of their use differ significantly depending on the mode's specialization.

### General Principles

1. **Adaptation to Specialization**: Each mode uses common tools in its own way, adapting them to its role. For example, `read_file` for the Architect mode serves to understand code architecture, while for Debug, it's used to locate errors.

2. **Semantic Difference**: Despite identical call syntax, the semantics of tool usage change between modes. For instance, `search_files` for the Code mode looks for patterns for refactoring, while for Test, it searches for test coverage.

3. **Usage Sequence**: Depending on the mode, tools are applied in different sequences to achieve the goal. For example:

   **Example for Code mode:**
   ```
   read_file → list_code_definition_names → search_files → apply_diff → execute_command
   ```

   **Example for Debug mode:**
   ```
   execute_command (get logs) → read_file → search_files → apply_diff → execute_command (verify)
   ```

### Key Examples

- **Task** uses `execute_command` at the beginning of its work in a specific way: the command `pwd && tree --gitignore && npx -y envinfo --markdown && npm run` is executed mandatorily to gather context before analyzing the task.

- **Orchestrator** applies `use_mcp_tool` strategically, according to the `MCP USAGE STRATEGY`, to gather necessary context before decomposing the task into subtasks for other modes.

- Some modes have limited use of the `new_task` tool. For example, Code can delegate tasks only to the Debug mode, while Orchestrator can delegate tasks to all modes.

### 🛡️ Principle of Self-Containment and Explicitness in System Prompts

All system prompts in Roo **must be fully self-contained**.

- **Never reference external files, specifications, or documentation** (e.g., “as per ARCHITECT-MODE.md” or “see tools.md”).
- The LLM only sees the prompt text.
- **All tool definitions, usage rules, schemas, and concrete examples must be present in the prompt itself.**
- Do not assume the LLM has access to any context except the system prompt text.

**Every tool and MCP server definition in a system prompt must:**
- Include a clear description, parameter list, and at least one correct usage example (and, if possible, a common incorrect example).
- For MCP tools, include input schemas and step-by-step usage patterns directly in the prompt.

