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
