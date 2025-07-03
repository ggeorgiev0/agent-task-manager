# Task 05: Output Parsing and Status Detection

## Task Description
Implement intelligent parsing of Claude Code output to automatically detect agent status (WORKING, REQUIRES ACTION, ERROR). This system monitors terminal output in real-time and updates the agent table to provide instant visual feedback about which agents need user attention.

## Priority: P0 (Must Have)
## Estimated Effort: 3-4 days

## Acceptance Criteria
- [ ] Real-time parsing of Claude Code output streams
- [ ] Accurate detection of "REQUIRES ACTION" prompts
- [ ] Reliable "WORKING" status during agent activity
- [ ] Clear "ERROR" status for failures
- [ ] Status updates within 500ms of output changes
- [ ] Pattern matching is configurable and extensible
- [ ] Historical output analysis for status determination
- [ ] No false positives for action requirements
- [ ] Status persistence during view switches
- [ ] Debug mode to see pattern matching in action

## Technical Implementation Notes

### Status Detection Architecture
```typescript
enum AgentStatus {
  IDLE = 'idle',
  WORKING = 'working',
  REQUIRES_ACTION = 'requires-action',
  ERROR = 'error'
}

interface StatusPattern {
  pattern: RegExp;
  status: AgentStatus;
  priority: number; // Higher priority patterns override lower
}

class StatusDetector {
  private patterns: StatusPattern[];
  private outputBuffer: string[];
  private lastActivity: Date;
  
  analyzeOutput(newOutput: string): AgentStatus;
  getStatusWithContext(): { status: AgentStatus, context: string };
  addCustomPattern(pattern: StatusPattern): void;
}
```

### Pattern Categories

#### REQUIRES ACTION Patterns
```javascript
const actionPatterns = [
  /Do you want to proceed\? \(y\/n\)/i,
  /Please select an option:/i,
  /Waiting for user input/i,
  /Permission required to:/i,
  /Would you like to:/i,
  /Choose one of the following:/i,
  /Press any key to continue/i,
  /\[Y\/n\]/,
  /\(yes\/no\)/i,
  /Requires your approval/i
];
```

#### ERROR Patterns
```javascript
const errorPatterns = [
  /^Error:/im,
  /^Failed:/im,
  /^Fatal:/im,
  /^Exception:/im,
  /^Traceback \(most recent call last\):/m,
  /^SyntaxError:/m,
  /^TypeError:/m,
  /command not found/i,
  /permission denied/i,
  /ENOENT:|EACCES:/
];
```

#### WORKING Indicators
- New output received within last 2 seconds
- Spinner characters detected (⠋⠙⠹⠸⠼⠴⠦⠧⠇⠏)
- Progress indicators ([■■■□□□])
- Continuous output stream

## Dependencies on Other Tasks
- Task 02: Terminal Integration (for output access)
- Task 04: Agent Management (for status updates)
- Updates: Task 03 (UI Components) with status

## Subtasks

### 5.1 Pattern System Design (3 hours)
- Define StatusPattern interface
- Create pattern registry system
- Implement priority-based matching
- Design pattern configuration format
- Plan for user-customizable patterns

### 5.2 Core Detection Engine (5 hours)
- Implement StatusDetector class
- Create output buffer management
- Implement pattern matching logic
- Handle multi-line pattern matching
- Add context extraction for matches

### 5.3 Real-time Output Analysis (4 hours)
- Hook into terminal output stream
- Implement incremental parsing
- Handle ANSI escape sequence filtering
- Optimize for performance
- Implement debouncing for rapid updates

### 5.4 Action Detection Patterns (4 hours)
- Research Claude Code prompt patterns
- Implement comprehensive action patterns
- Test with real Claude Code outputs
- Handle edge cases and variations
- Add confidence scoring

### 5.5 Error Detection Logic (3 hours)
- Implement error pattern matching
- Distinguish errors from warnings
- Extract error context/messages
- Handle stack traces
- Detect compilation/runtime errors

### 5.6 Working Status Detection (3 hours)
- Implement activity timeout logic
- Detect spinner/progress indicators
- Monitor output frequency
- Handle idle vs working states
- Implement pulse animation trigger

### 5.7 Status State Machine (3 hours)
- Design status transition rules
- Implement state persistence
- Handle conflicting patterns
- Add hysteresis to prevent flapping
- Log status transitions

### 5.8 Pattern Configuration (2 hours)
- Create pattern config file format
- Implement pattern hot-reloading
- Add pattern validation
- Create default pattern sets
- Document pattern syntax

### 5.9 Debug and Testing Tools (3 hours)
- Create pattern testing interface
- Add debug logging for matches
- Implement pattern playground
- Create test harness with sample outputs
- Add performance profiling

## Success Metrics
- Status detection accuracy > 95%
- Detection latency < 100ms
- Zero false positives for action prompts
- Pattern matching < 5ms per output line
- Memory usage < 10MB for pattern engine

## Pattern Examples

### Complex Multi-line Patterns
```typescript
// Git conflict detection
const gitConflictPattern = {
  pattern: /<<<<<<< HEAD[\s\S]*?=======/m,
  status: AgentStatus.REQUIRES_ACTION,
  priority: 10
};

// Multi-choice prompt
const choicePattern = {
  pattern: /\[1\].*\n\[2\].*\n.*choice:/im,
  status: AgentStatus.REQUIRES_ACTION,
  priority: 8
};
```

### Context Extraction
```typescript
interface StatusContext {
  status: AgentStatus;
  matchedPattern: string;
  context: string; // Surrounding lines
  confidence: number; // 0-1
  timestamp: Date;
}
```

## Testing Scenarios

### Test Case 1: Rapid Status Changes
- Generate output alternating between patterns
- Verify no status flapping
- Ensure final status is correct

### Test Case 2: Ambiguous Output
- Output containing both error and prompt
- Verify priority system works
- Check context extraction

### Test Case 3: Performance Test
- Stream 1000 lines/second
- Verify < 5% CPU usage
- Check memory stability

## Risk Mitigation
- **Risk**: Pattern false positives
  - **Mitigation**: Extensive testing, confidence scoring
- **Risk**: Performance degradation
  - **Mitigation**: Optimize regex, limit buffer size
- **Risk**: Missing new patterns
  - **Mitigation**: User-configurable patterns, logging

## Future Enhancements
- Machine learning for pattern detection
- User-contributed pattern library
- Pattern effectiveness analytics
- Natural language pattern definition
- Integration with Claude Code updates

## Notes
- Consider caching compiled regex patterns
- Log unmatched suspicious outputs
- Implement pattern effectiveness tracking
- Plan for internationalization
- Consider semantic analysis for complex prompts