# Task 04: Claude Code Process Spawning and Management

## Task Description
Implement the core functionality for spawning and managing Claude Code processes. This includes creating new agent instances, managing their lifecycle, handling process communication, and coordinating between multiple running agents.

## Priority: P0 (Must Have)
## Estimated Effort: 4-5 days

## Acceptance Criteria
- [ ] Claude Code processes can be spawned in specific directories
- [ ] Each agent runs in its own isolated terminal session
- [ ] Agents can be started with proper environment variables
- [ ] Process lifecycle is properly managed (start, stop, restart)
- [ ] Agent crashes are detected and handled gracefully
- [ ] Multiple agents can run simultaneously without interference
- [ ] Agent output is properly captured and buffered
- [ ] Working directory changes are tracked per agent
- [ ] Claude Code availability is verified on startup
- [ ] Clear error messages when Claude Code is not found

## Technical Implementation Notes

### Process Architecture
```typescript
interface AgentProcess {
  id: string;
  process: ChildProcess;
  pty: IPty;
  workingDirectory: string;
  status: AgentStatus;
  startTime: Date;
  outputBuffer: CircularBuffer;
  metadata: {
    project: string;
    branch: string;
  };
}

class AgentManager {
  private agents: Map<string, AgentProcess>;
  
  spawnAgent(workingDir: string): Promise<string>;
  killAgent(id: string): Promise<void>;
  restartAgent(id: string): Promise<void>;
  getAgentOutput(id: string, lines?: number): string[];
  switchToAgent(id: string): void;
}
```

### Claude Code Integration
- Spawn using: `claude-code` command
- Pass working directory as argument
- Capture both stdout and stderr
- Handle interactive prompts
- Monitor exit codes

## Dependencies on Other Tasks
- Task 02: Terminal Integration (must be complete)
- Task 03: UI Components (for displaying agents)
- Provides data for: Task 05 (Status Detection)

## Subtasks

### 4.1 Claude Code Detection (3 hours)
- Check if `claude-code` is in PATH
- Verify Claude Code version compatibility
- Display clear error if not found
- Provide installation instructions
- Cache detection result for performance

### 4.2 Agent Process Architecture (4 hours)
- Design AgentManager singleton service
- Implement agent registry with Map
- Create AgentProcess class/interface
- Define agent lifecycle states
- Implement event emitter for agent events

### 4.3 Process Spawning (6 hours)
- Implement spawn method with PTY
- Set proper environment variables
- Handle working directory setup
- Configure process options (shell, encoding)
- Implement unique ID generation
- Add process to agent registry

### 4.4 Process Lifecycle Management (5 hours)
- Implement graceful shutdown
- Handle force kill after timeout
- Detect and handle process crashes
- Implement restart functionality
- Clean up resources on termination
- Handle zombie processes

### 4.5 Output Management (4 hours)
- Implement circular buffer for output
- Configure buffer size limits
- Handle output encoding properly
- Implement output streaming to terminal
- Add output search functionality
- Handle ANSI escape sequences

### 4.6 Working Directory Tracking (3 hours)
- Monitor `cd` commands in output
- Update working directory state
- Handle relative path resolution
- Validate directory existence
- Sync with git detection

### 4.7 Multi-Agent Coordination (4 hours)
- Implement agent switching logic
- Ensure single active input stream
- Handle concurrent output streams
- Implement agent isolation
- Prevent resource conflicts

### 4.8 Error Handling (3 hours)
- Handle spawn failures gracefully
- Implement retry logic for crashes
- Log errors appropriately
- Provide user-friendly error messages
- Handle edge cases (permissions, etc.)

### 4.9 Agent Metadata Management (2 hours)
- Extract project information
- Detect git branch information
- Update metadata on directory changes
- Cache metadata for performance
- Handle non-git directories

## Success Metrics
- Agent spawn time < 1 second
- Zero agent interference issues
- Crash recovery within 2 seconds
- Output buffer handles 10MB+ efficiently
- Memory usage < 50MB per agent

## Technical Challenges

### Challenge 1: Process Communication
- **Issue**: Managing bidirectional communication with Claude Code
- **Solution**: Use PTY for full terminal emulation, handle all escape sequences

### Challenge 2: Resource Management
- **Issue**: Memory and CPU usage with multiple agents
- **Solution**: Implement output buffering limits, CPU throttling if needed

### Challenge 3: Process Cleanup
- **Issue**: Ensuring clean termination without orphan processes
- **Solution**: Track all child processes, implement cleanup on app exit

### Challenge 4: Working Directory Detection
- **Issue**: Accurately tracking directory changes
- **Solution**: Parse PS1 prompts, monitor pwd commands, validate paths

## Error Scenarios

### Scenario 1: Claude Code Not Found
```typescript
Error: Claude Code CLI not found
Please install Claude Code:
- macOS/Linux: curl -sSL https://claude.ai/install | bash
- Or follow manual installation at: https://claude.ai/docs
```

### Scenario 2: Agent Crash
```typescript
Error: Agent terminated unexpectedly
Exit code: 1
Last output: [show last 10 lines]
[Restart] [View Logs] [Dismiss]
```

### Scenario 3: Spawn Failure
```typescript
Error: Failed to start agent
Reason: Permission denied for directory /path/to/dir
[Retry] [Choose Different Directory] [Cancel]
```

## Testing Requirements
- Unit tests for AgentManager methods
- Integration tests for process spawning
- Stress tests with 5+ concurrent agents
- Test crash recovery mechanisms
- Test resource cleanup

## Risk Mitigation
- **Risk**: Claude Code API changes
  - **Mitigation**: Version detection, compatibility layer
- **Risk**: Resource exhaustion
  - **Mitigation**: Agent limits, resource monitoring
- **Risk**: Security issues with process spawning
  - **Mitigation**: Validate all inputs, use safe spawn options

## Notes
- Consider implementing agent templates for common setups
- Log all agent lifecycle events for debugging
- Consider adding agent CPU/memory monitoring
- Plan for future agent pooling/reuse
- Document expected Claude Code behavior