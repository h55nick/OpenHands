# Proposal: Integrating Microagent Lookups into Resolver Workflow

## Overview
This proposal explores the feasibility and implementation approach for integrating microagent lookups into the resolver workflow.

## Current Architecture

### 1. Resolver Workflow
- The resolver workflow is built around the `IssueHandlerInterface` which provides a standardized way to handle issues across different platforms (GitHub, GitLab)
- Key operations include:
  - Issue downloading and conversion
  - Comment handling
  - Pull request creation
  - Branch management
  - External issue reference handling

### 2. Microagent System
- Microagents are specialized prompts that provide:
  - Domain-specific knowledge
  - Repository-specific context
  - Task-specific workflows
- They are stored in `.openhands/microagents/` directory
- Can be either public (general-purpose) or repository-specific

## Integration Analysis

### Feasibility: HIGH
The integration appears highly feasible for several reasons:
1. The resolver workflow already has hooks for context gathering (e.g., `get_context_from_external_issues_references`)
2. Microagents are designed to provide contextual information
3. The system already supports repository-specific configurations

### Implementation Approach

#### 1. New Interface Methods
Add to `IssueHandlerInterface`:
```python
@abstractmethod
def get_microagent_context(self, issue: Issue) -> list[str]:
    """Get relevant microagent context for the given issue."""
    pass
```

#### 2. Context Integration Points
- Pre-analysis: Before issue processing to determine relevant microagents
- During PR creation: To include repository-specific guidelines
- During review: To provide specialized review context

#### 3. Microagent Selection Logic
- Scan issue title/body for relevant keywords
- Check repository-specific microagents
- Match against public microagent registry

### Required Changes

#### 1. Core Components
- Add microagent lookup service
- Extend issue handler implementations
- Add microagent context to PR creation flow

#### 2. New Components
- Microagent matcher (keyword-based)
- Context aggregator (combine multiple microagent outputs)
- Cache layer for frequently used microagents

#### 3. Configuration
- Add microagent priority settings
- Configure context inclusion rules
- Set up caching parameters

## Effort Estimation

**Overall Complexity: MEDIUM**

Breakdown:
- Core integration: ~2-3 days
- Testing & validation: ~2 days
- Documentation: ~1 day

Total estimated effort: 5-6 developer days

## Benefits
1. More intelligent issue handling with domain knowledge
2. Repository-specific best practices enforcement
3. Improved PR quality through specialized review context
4. Reduced need for manual intervention

## Risks
1. Performance impact from additional context lookups
2. Potential for conflicting guidance from multiple microagents
3. Need for careful caching to avoid stale context

## Next Steps
1. Create detailed technical design document
2. Implement proof of concept with single microagent
3. Add tests for microagent context integration
4. Gradually expand to multiple microagents with priority system