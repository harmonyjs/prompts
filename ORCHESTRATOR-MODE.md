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
