# How to Customize Prompts for Your Project

This guide explains how to customize prompts in OpenHands using microagents. Microagents allow you to extend and customize the behavior of OpenHands agents through markdown files with special frontmatter headers.

## Understanding Microagent Types

OpenHands supports three types of microagents, as defined in the MicroAgentType enum:

1. **Repository Knowledge (`repo`)**: 
   - Provides repository-wide instructions that are loaded every time an agent works with your repository
   - Typically stored in `.openhands/microagents/repo.md`
   - Used for repository-specific guidelines, team practices, and project workflows
   - Automatically loaded when working with the repository
   - Can be private to a repository or shared in the OpenHands core

2. **Knowledge (`knowledge`)**:
   - Adds contextual knowledge triggered by specific keywords
   - Helps with language best practices, framework guidelines, common patterns, and tool usage
   - Must define trigger words that activate the knowledge (case-insensitive matching)
   - Loaded when trigger words appear in the conversation
   - Can be reused across multiple projects

3. **Task (`task`)**:
   - Defines interactive workflows that guide users through common development tasks
   - Accepts structured input parameters with validation
   - Follows predefined steps that adapt to context
   - Ensures consistent outcomes for common operations
   - Can be shared or repository-specific

## Microagent File Structure

Each microagent is defined in a markdown file with YAML frontmatter. Here's the structure:

```yaml
---
name: [required] Name of the microagent (should match filename for better organization)
type: [required] One of: repo, knowledge, task
version: [optional] Version number (default: 1.0.0)
agent: [optional] Agent type to use (default: CodeActAgent)
triggers: [required for knowledge type] List of trigger words
---

[Markdown content providing instructions or knowledge]
```

### Metadata Fields

The MicroAgentMetadata class defines the following fields:

- `name`: (required) Identifier for the microagent (defaults to "default")
- `type`: (required) Must be one of: `repo`, `knowledge`, or `task` (defaults to "knowledge")
- `version`: (optional) Version tracking (defaults to "1.0.0")
- `agent`: (optional) Agent implementation to use (defaults to "CodeActAgent")
- `triggers`: (required for knowledge type) List of trigger words for knowledge microagents (defaults to empty list)

### Task Input Schema

For task microagents, input parameters are defined in the frontmatter using a YAML schema. Here's an example:

```yaml
---
name: update_pr_description
type: task
version: 1.0.0
agent: CodeActAgent
inputs:
  - name: PR_URL                    # Name of the parameter
    description: URL of the pull request  # Description for users
    type: string                    # Parameter type
    required: true                  # Whether it's required
    validation:                     # Optional validation rules
      pattern: ^https://github.com/.+/.+/pull/[0-9]+$
  - name: BRANCH_NAME
    description: Branch name corresponds to the pull request
    type: string
    required: true
---
```

The schema supports:
- `name`: Parameter identifier
- `description`: Human-readable description
- `type`: Data type (e.g., string)
- `required`: Whether the parameter is mandatory (defaults to true)
- `validation`: Optional validation rules (e.g., regex patterns)

OpenHands includes several task agents for common operations:
- `update_pr_description.md`: Updates PR descriptions
- `address_pr_comments.md`: Handles PR comment responses
- `get_test_to_pass.md`: Helps fix failing tests
- `update_test_for_new_implementation.md`: Updates tests for new code
- `add_openhands_repo_instruction.md`: Adds repository instructions

## Examples

### Examples from OpenHands

You can find real-world examples in the OpenHands repository:

1. **Repository Knowledge**: See [OpenHands's repo.md](https://github.com/All-Hands-AI/OpenHands/blob/main/.openhands/microagents/repo.md)
   - Repository-specific guidelines
   - Team practices and conventions
   - Project workflows

2. **Knowledge Microagent**: See [github.md](https://github.com/All-Hands-AI/OpenHands/tree/main/microagents/knowledge/github.md)
   - Git and GitHub best practices
   - Triggered by keywords like "github" and "git"
   - Reusable across projects

3. **Task Microagent**: See [update_pr_description.md](https://github.com/All-Hands-AI/OpenHands/tree/main/microagents/tasks/update_pr_description.md)
   - Interactive PR update workflow
   - Input validation
   - Step-by-step guidance

## Directory Structure

OpenHands loads microagents from two sources:

1. **Shareable Microagents (Public)**
   ```
   OpenHands/microagents/
   ├── knowledge/     # Keyword-triggered expertise
   │   ├── git.md      # Git operations
   │   ├── testing.md  # Testing practices
   │   └── docker.md   # Docker guidelines
   └── tasks/        # Interactive workflows
       ├── pr_review.md   # PR review process
       ├── bug_fix.md     # Bug fixing workflow
       └── feature.md     # Feature implementation
   ```

2. **Repository Instructions (Private)**
   ```
   your-repository/
   └── .openhands/
       └── microagents/
           ├── repo.md      # Repository-specific instructions
           ├── knowledge/   # Private knowledge microagents
           └── tasks/       # Private task microagents
   ```

## Loading Behavior

When OpenHands works with a repository, it:

1. **Repository Microagents**:
   - Loads repository-specific instructions from `.openhands/microagents/repo.md` if present
   - Legacy instructions in `.openhands_instructions` are also supported
   - Only one repository microagent should be active at a time

2. **Knowledge Microagents**:
   - Loaded when their trigger words appear in conversations (case-insensitive matching)
   - Multiple knowledge microagents can be active simultaneously
   - Both public (from OpenHands core) and private (from repository) agents are considered

3. **Task Microagents**:
   - Loaded when specific tasks are requested
   - Can define input parameters with validation
   - Support both shared workflows and repository-specific tasks

## Best Practices

1. **For Knowledge Agents**:
   - Choose distinctive triggers that avoid unintended activations
   - Focus on one area of expertise
   - Include practical examples
   - Use file patterns when relevant
   - Keep knowledge general and reusable

2. **For Task Agents**:
   - Break workflows into clear steps
   - Validate user inputs using the TaskInput schema
   - Provide helpful defaults
   - Include usage examples
   - Make steps adaptable to different scenarios

3. **For Repository Agents**:
   - Document clear setup instructions
   - Include repository structure details
   - Specify testing and build procedures
   - List environment requirements
   - Maintain up-to-date team practices

4. **General Guidelines**:
   - Use descriptive names that match the microagent's purpose
   - Keep instructions clear and concise
   - Use markdown formatting for better readability
   - Update version numbers for significant changes
   - Test thoroughly with various inputs and scenarios

## Contributing

### When to Create a Microagent

1. **Knowledge Agents** - When you have:
   - Language/framework best practices
   - Tool usage patterns
   - Common problem solutions
   - General development guidelines

2. **Task Agents** - When you have:
   - Repeatable workflows
   - Multi-step processes
   - Common development tasks
   - Standard procedures

3. **Repository Agents** - When you need:
   - Project-specific guidelines
   - Team conventions and practices
   - Custom workflow documentation
   - Repository-specific setup instructions

### Submission Process

1. Create your agent file in the appropriate directory:
   - `knowledge/` for expertise (public, shareable)
   - `tasks/` for workflows (public, shareable)
   - Note: Repository agents should remain in their respective repositories' `.openhands/microagents/` directory

2. Test thoroughly with various inputs and scenarios

3. Submit a pull request to OpenHands for public agents

## Limitations and Notes

1. **Repository Microagents**:
   - Only one repository microagent can be active at a time
   - Legacy `.openhands_instructions` files are still supported
   - Can be overridden by placing a microagent with the same name in your repository

2. **Knowledge Microagents**:
   - Triggers are matched case-insensitively
   - Multiple knowledge microagents can be active simultaneously
   - Both public and private agents are considered

3. **Task Microagents**:
   - Should have unique names within a project
   - Must properly validate input parameters
   - Can be shared or repository-specific