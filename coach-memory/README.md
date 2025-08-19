# Coach Memory System

This directory contains the intelligent memory system for the Coach agent orchestration framework.

## Structure

- **context.json** - Main context file (kept lean, <10KB)
- **decisions/** - Versioned decision history with rationale
- **outputs/** - Agent work products organized by agent
- **history/** - Session logs and audit trails
- **snapshots/** - Context checkpoints for recovery
- **cache/** - Frequently accessed data for performance
- **tasks/** - Detailed task-specific contexts

## Key Principles

1. **Lean Active Memory**: The main context.json stays small with references to detailed data
2. **Atomic Updates**: Every action updates the relevant context sections
3. **File References**: Large data stored in files, referenced by path
4. **Kanban Flow**: Tasks move through states triggering context updates
5. **Version Control**: All decisions and changes are tracked with timestamps

## Usage

The Coach agent is the ONLY entity that should directly modify these files. All agent communications flow through Coach, which manages this memory system to ensure:

- Perfect context continuity across sessions
- No context drift between agents
- Complete audit trail of all decisions
- Efficient memory usage through lazy loading
- Fast access to frequently used contexts

## Memory Lifecycle

1. **Session Start**: Initialize or load existing context
2. **Task Assignment**: Update working memory and kanban
3. **Agent Work**: Track current agent and task state
4. **Decision Points**: Create decision records with references
5. **Task Completion**: Archive outputs and update history
6. **Session End**: Create snapshot and optimize memory

## Performance

- Context loads in <100ms
- Atomic updates in <50ms
- Full history available via references
- Smart caching of hot paths
- Automatic compression when needed