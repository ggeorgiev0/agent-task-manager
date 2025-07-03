# Task 07: Session Persistence - Save/Restore Functionality

## Task Description
Implement session persistence to save the state of all agent terminals when the application closes and restore them when it reopens. This includes saving working directories, window dimensions, UI state, and user preferences. Sessions should be restored without automatically restarting agents (manual control retained).

## Priority: P0 (Must Have)
## Estimated Effort: 2-3 days

## Acceptance Criteria
- [ ] Working directories saved for all agents on app close
- [ ] Window size and position saved and restored
- [ ] UI state preserved (divider position, theme, etc.)
- [ ] Sessions restored on app launch without auto-starting agents
- [ ] Corrupted session files handled gracefully
- [ ] Session file format is versioned for future compatibility
- [ ] User preferences persisted separately from sessions
- [ ] Option to clear session data
- [ ] Multiple session file backups maintained
- [ ] Cross-platform session file locations

## Technical Implementation Notes

### Session Data Structure
```typescript
interface SessionData {
  version: string;
  timestamp: string;
  agents: AgentSession[];
  ui: UISession;
  preferences: UserPreferences;
}

interface AgentSession {
  id: string;
  workingDirectory: string;
  project: string;
  branch: string;
  lastActive: string;
  terminalSize?: {
    cols: number;
    rows: number;
  };
}

interface UISession {
  windowBounds: {
    x: number;
    y: number;
    width: number;
    height: number;
  };
  terminalHeightPercent: number;
  theme: 'light' | 'dark' | 'auto';
  activeAgentId?: string;
}

interface UserPreferences {
  shortcuts: Record<string, string>;
  terminal: {
    fontSize: number;
    fontFamily: string;
    scrollback: number;
  };
  general: {
    confirmBeforeClose: boolean;
    maxAgents: number;
  };
}
```

### Storage Architecture
- Use application data directory
- JSON format for human readability
- Atomic writes to prevent corruption
- Automatic backups with rotation

## Dependencies on Other Tasks
- Task 04: Agent Management (provides agent state)
- Task 03: UI Components (provides UI state)
- Enhances: All features with persistence

## Subtasks

### 7.1 Storage System Design (3 hours)
- Define session data schema
- Choose storage location strategy
- Design file naming convention
- Plan backup rotation system
- Implement version migration plan

### 7.2 Session Manager Implementation (4 hours)
- Create SessionManager service
- Implement save/load methods
- Add data validation
- Handle schema versioning
- Implement backup management

### 7.3 Application State Collection (4 hours)
- Collect agent states
- Gather UI configuration
- Extract window dimensions
- Compile user preferences
- Handle missing data gracefully

### 7.4 Save Trigger Implementation (3 hours)
- Hook into app close event
- Implement periodic auto-save
- Add manual save option
- Ensure save completes before exit
- Handle save failures

### 7.5 Session Restoration (4 hours)
- Load session on app start
- Validate session data
- Restore window dimensions
- Recreate agent entries
- Apply UI preferences

### 7.6 File System Operations (3 hours)
- Implement atomic file writes
- Create backup rotation
- Handle file permissions
- Implement file locking
- Add compression option

### 7.7 Error Handling (2 hours)
- Handle corrupted files
- Implement fallback defaults
- Add recovery options
- Log errors appropriately
- Show user-friendly messages

### 7.8 Preferences Management (3 hours)
- Separate preferences from session
- Implement preference editor
- Add import/export options
- Validate preference values
- Handle preference conflicts

## Success Metrics
- Session save time < 100ms
- Session load time < 200ms
- Zero data loss on proper shutdown
- Successful recovery from corrupted files
- Backward compatibility maintained

## File Locations

### macOS
```
~/Library/Application Support/AgentTaskManager/
├── session.json           # Current session
├── session.backup.json    # Previous session
├── preferences.json       # User preferences
└── backups/              # Rotating backups
    ├── session-2024-01-01.json
    └── ...
```

### Linux
```
~/.config/agent-task-manager/
├── session.json
├── session.backup.json
├── preferences.json
└── backups/
```

## Implementation Details

### Atomic File Writing
```typescript
async function atomicWrite(filepath: string, data: string) {
  const tempFile = `${filepath}.tmp`;
  await fs.writeFile(tempFile, data);
  await fs.rename(tempFile, filepath);
}
```

### Session Validation
```typescript
function validateSession(data: unknown): SessionData {
  // Check version compatibility
  // Validate required fields
  // Ensure data integrity
  // Handle missing optional fields
  // Migrate old formats
}
```

### Backup Rotation
```typescript
interface BackupConfig {
  maxBackups: number;
  backupInterval: number; // minutes
  compressionEnabled: boolean;
}
```

## Error Recovery Strategies

### Strategy 1: Corrupted Session File
1. Try to parse partial data
2. Load previous backup
3. Start with clean session
4. Notify user of recovery

### Strategy 2: Permission Errors
1. Try alternative location
2. Use in-memory session
3. Warn user about persistence
4. Provide fix instructions

### Strategy 3: Disk Space Issues
1. Clean old backups
2. Compress session data
3. Reduce backup frequency
4. Alert user

## Testing Requirements
- Unit tests for serialization/deserialization
- Integration tests for save/load cycle
- Corruption recovery tests
- Cross-platform file location tests
- Performance tests with large sessions

## Security Considerations
- No sensitive data in sessions
- File permissions set appropriately
- Path traversal prevention
- Input validation on load

## Migration Strategy
```typescript
const migrations = {
  '1.0.0': (data: any) => data,
  '1.1.0': (data: any) => ({
    ...data,
    preferences: data.preferences || defaultPreferences
  }),
  // Future migrations
};
```

## Risk Mitigation
- **Risk**: Data loss on crash
  - **Mitigation**: Periodic auto-save, multiple backups
- **Risk**: Session file corruption
  - **Mitigation**: Atomic writes, validation, backups
- **Risk**: Breaking changes
  - **Mitigation**: Version field, migration system

## Future Enhancements
- Cloud sync for sessions
- Session templates/profiles
- Session sharing between machines
- Encrypted session storage
- Session history browser

## Notes
- Consider SQLite for future complex needs
- Plan for session file size limits
- Document session file format
- Consider telemetry for session stats
- Allow session export for debugging