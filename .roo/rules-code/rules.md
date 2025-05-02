None.

====

IMPORTANT INSTRUCTIONS FOR CODE WRITING

## How to Analyze

1. Thoroughly examine the input data, task requirements, and expected outcomes; break the problem into clearly defined subtasks.
2. Identify the specific files that must be modified and evaluate how each change will impact the overall project.
3. Keep in mind whole-project consistency and ensure that your solution aligns with the existing codebase and architecture.

## How to Act

1. Implement only what is necessary to complete the task; avoid incorporating extraneous features or improvements.
2. Write clear, idiomatic TypeScript/JavaScript code that is immediately understandable without requiring additional explanation.
3. Always heavily comment your code to explain the rationale behind each segment, focusing on why it exists rather than merely describing what it does. Use JSdoc and ASCII art to improve expressiveness of explanations.
4. Adhere strictly to the existing coding style (indentation, quotes, imports, etc.).
5. Divide your code into small, self-contained modules, functions, or files, and include detailed comments that explain each step and the logic behind your solution.
6. Never skip any steps or high-level plan points; ensure that your solution aligns with the user's expectations.
7. Try to write pure functions without implicit side effects; if side effects are unavoidable, clearly document them in comments. For example, strictly avoid modifying global variables or mutating objects passed as arguments.
8. Always update unit tests to reflect the changes you have made, ensuring that they cover all possible edge cases and scenarios.
9. Avoid over-abstraction; only introduce additional layers of complexity when they are necessary to solve the problem.
10. Aim for code reusability by identifying common patterns and extracting them into reusable functions or modules.
11. Don't produce code files with more than 200 lines of code; if a file exceeds this limit, refactor it into smaller, more manageable files.
12. Never group functions into classes only with static methods; instead, use modules or namespaces to group related functions.

## How to deal with imports

1. Don't use relative paths in imports; instead, use absolute paths via # subpath alias.
2. Always use node: protocol prefixes for built-in Node.js modules.
3. Avoid using dynamic imports; prefer static imports for better performance and readability.

## How to Write JavaScript Code

1. Avoid using forEach for iterations when possible; prefer for...of or map for better readability and potential performance benefits.
2. Never use return await statements; instead, directly return the awaited promise to avoid unnecessary overhead.
3. Ensure that error handling is consistent across all functions, logging errors and re-throwing them when necessary.
4. Prefer functional programming to object-oriented programming when possible; use pure functions and avoid side effects.
5. Don't overuse Object.freeze or Object.seal! Use it rarely and only when necessary. Each use must be justified in comments.
6. Never introduce «Argument Forwarding» or «Parameter Drilling» anti-patterns; instead, try to refactor the code to avoid passing arguments through multiple functions.

## How to Write TypeScript Code

0. Type safety is crucial and non-negotiable; ensure that your code is fully typed, utilizing generics, interfaces, and type aliases wherever possible.
1. Eliminate implicit any types; if their use is unavoidable, provide a clear explanation in comments and mute the ESLint rule for that specific line.
2. Avoid unjustified type assertions (using `as`); instead, use appropriate generics and explicitly specify return types.
3. Use import type exclusively when importing types to ensure efficient tree-shaking and optimized bundling.
4. Always separate types/interfaces into dedicated files, and ensure that they are correctly exported and imported.
5. Prefer type over interface for defining object shapes and interface for defining contracts or extending other interfaces.
6. Use explicit .ts extensions for imports to avoid ambiguity and ensure that TypeScript can correctly resolve the module path.
7. Avoid casting return values via return VAL as TYPE; instead, ensure that the function returns the correct type directly.
8. Don't mix types and functions in the same file; separate them into distinct sections or files for better organization.
9. Never use Non-Null Assertion in Type Assertions (e.g., `variable!`); instead, use optional chaining or nullish coalescing to handle potential null values.

## How to Write Tests

1. Never group tests into __tests__ directories; instead, place them alongside the code they test.

## How to Structure Code

1. Always use kebab-case for file names and directories.
2. Don't respect domain-driven design principles if it leads to over-engineering; keep the codebase simple and maintainable.

====

## MCP Server Usage Strategy

**Overarching Principle: Proactive & Informed Context Gathering**

Your core function is to complete the user's task effectively within your designated role (e.g., coding, answering questions, debugging, architecting, orchestrating). Do not rely solely on your internal knowledge base, especially when dealing with specific technologies, complex codebases, or rapidly evolving information. If fetching external context via MCP could reasonably improve your understanding, the accuracy of your response, the quality of your generated code, or the safety/effectiveness of your actions, you **should** prioritize doing so. Always prefer verified, current information over assumptions.

**Decision Flow Algorithm:**

*   **Input:** User Request/Task, Current Workspace Context (`environment_details`), Results from previous steps.
*   **Output:** Decision on whether to use an MCP tool, and if so, which one and with what parameters, to better fulfill the current task.

**Steps:**

1.  **Initial Analysis:**
    *   Thoroughly analyze the assigned task and any provided context.
    *   Identify the core goal, mentioned technologies (libraries, frameworks, APIs, etc.), apparent complexity, and potential relevance of the codebase or external information.

2.  **Check for Explicit Technology Mentions -> Use `context7` (Mandatory):**
    *   **Condition:** Does the task involve understanding or using *explicitly named* software technologies?
    *   **Action (Mandatory):** If YES, for *each* named technology relevant to completing your *current step*:
        *   **Step 2a:** Call `use_mcp_tool` (server: `context7`, tool: `resolve-library-id`, args: `{"libraryName": "TechnologyName"}`).
        *   **Step 2b:** Wait for the result. If successful, extract the `context7CompatibleLibraryID`.
        *   **Step 2c:** Call `use_mcp_tool` (server: `context7`, tool: `get-library-docs`, args: `{"context7CompatibleLibraryID": "ID_from_step_2b", "topic": "OptionalRelevantTopic"}`). Use a relevant `topic` if the task focuses on a specific aspect.
        *   **Step 2d:** Wait for the documentation result.
    *   **Rationale:** Ensures actions (coding, answering, planning) involving specific technologies are based on official, current documentation.
    *   **Example (Code Mode):** Task: "Write a function using Axios to fetch data." -> **Must** use `context7` for "Axios".
    *   **Example (Ask Mode):** Task: "Explain how middleware works in Express." -> **Must** use `context7` for "Express".

3.  **Assess Need for Codebase Overview -> Consider `repomix` (Recommended):**
    *   **Condition:** Does completing the task require understanding the structure or content of a significant portion of the *local codebase*? Is the task complex, potentially affecting multiple files? Is a high-level view needed before diving into specific files?
    *   **Action (Recommended):** If YES, **should** consider using `repomix`'s `pack_codebase` tool.
        *   **Step 3a:** Call `use_mcp_tool` (server: `repomix`, tool: `pack_codebase`, args: `{"directory": "/absolute/path/to/workspace", "compress": true, "ignorePatterns": "node_modules/**,dist/**,build/**,vendor/**,**/*.log,*.lock"}`). *Obtain the absolute workspace path from SYSTEM INFORMATION.*
        *   **Step 3b:** Wait for the result.
    *   **Rationale:** Provides a snapshot of the codebase, aiding in understanding dependencies, identifying relevant modules, or planning changes before modifying code or providing architectural advice.
    *   **Example (Architect Mode):** Task: "Design a caching layer for the application." -> **Should** use `repomix` to understand current data flow and potential integration points.
    *   **Example (Code Mode):** Task: "Refactor the `utils` module." -> **Should** use `repomix` (perhaps with `includePatterns: "src/utils/**"`) to get the full context of the module.

4.  **Identify Need for External/Real-time Info -> Consider `tavily` (Situational):**
    *   **Condition:** Does completing the task require information *not typically found* in official documentation or the local codebase? Specific triggers include:
        *   Troubleshooting specific, non-obvious error messages encountered during execution or debugging.
        *   Finding current community best practices or design patterns relevant to the task.
        *   Checking for recent security vulnerabilities (CVEs) related to dependencies.
        *   Comparing alternative libraries or approaches not explicitly named by the user.
        *   Seeking tutorials, specific implementation examples, or configuration advice.
        *   Getting up-to-the-minute news or status updates impacting the task.
    *   **Action (Situational):** If YES, **should** consider using `tavily`'s `tavily-search`.
        *   **Step 4a:** Call `use_mcp_tool` (server: `tavily`, tool: `tavily-search`, args: `{"query": "Specific search query", "include_domains": ["optional_trusted_domains"], "time_range": "optional_time_limit", "topic": "general_or_news"}`). Tailor parameters for relevance.
        *   **Step 4b:** Wait for the search results.
    *   **Rationale:** Accesses real-time web information crucial for tasks involving troubleshooting, security, rapidly evolving technologies, or community-driven knowledge.
    *   **Example (Debug Mode):** Task: "Figure out why I'm getting `CORS error` when calling the API." -> **Should** use `tavily` to search for common causes and server/client configurations for CORS.
    *   **Example (Code Mode):** Task: "Implement this feature using the 'observer pattern'." -> **Should** use `tavily` to find current best-practice examples of the observer pattern in the target language/framework.

5.  **Synthesize & Proceed with Task:**
    *   Analyze the results obtained from any MCP calls made.
    *   Use this enriched context to perform your primary function more effectively.
    *   Decide if further context gathering (e.g., `read_file` on specific files identified via MCP) is needed before completing the current step of your task.

