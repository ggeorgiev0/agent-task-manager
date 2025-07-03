# Task 08: Keyboard Shortcuts - Hotkey Implementation

## Task Description
Implement a comprehensive keyboard shortcut system that allows users to efficiently control the application without using the mouse. This includes shortcuts for switching between agents, adding new terminals, and other common actions. The system should be customizable and work consistently across the application.

## Priority: P1 (Should Have)
## Estimated Effort: 2-3 days

## Acceptance Criteria
- [ ] Default shortcuts for switching agents (Cmd+1 through Cmd+5)
- [ ] Shortcut for adding new terminal (Cmd+T)
- [ ] Shortcuts work globally within app window
- [ ] Visual indicator showing current shortcuts
- [ ] Customizable shortcut configuration
- [ ] Conflict detection for custom shortcuts
- [ ] Shortcuts persist across sessions
- [ ] Help overlay showing all shortcuts (Cmd+?)
- [ ] Shortcuts work in all app states
- [ ] Platform-appropriate modifiers (Cmd on macOS, Ctrl on Linux)

## Technical Implementation Notes

### Shortcut System Architecture
```typescript
interface Shortcut {
  id: string;
  keys: string[]; // e.g., ['cmd', '1']
  action: string;
  description: string;
  customizable: boolean;
  context?: 'global' | 'terminal' | 'table';
}

class ShortcutManager {
  private shortcuts: Map<string, Shortcut>;
  private keyMap: Map<string, string>; // key combo -> action
  
  registerShortcut(shortcut: Shortcut): void;
  handleKeyPress(event: KeyboardEvent): boolean;
  updateShortcut(actionId: string, newKeys: string[]): void;
  getShortcutsForContext(context: string): Shortcut[];
}
```

### Default Shortcuts
```typescript
const defaultShortcuts = [
  // Agent switching
  { keys: ['cmd', '1'], action: 'switch-agent-1' },
  { keys: ['cmd', '2'], action: 'switch-agent-2' },
  { keys: ['cmd', '3'], action: 'switch-agent-3' },
  { keys: ['cmd', '4'], action: 'switch-agent-4' },
  { keys: ['cmd', '5'], action: 'switch-agent-5' },
  
  // Terminal management
  { keys: ['cmd', 't'], action: 'add-terminal' },
  { keys: ['cmd', 'w'], action: 'close-terminal' },
  
  // Navigation
  { keys: ['cmd', 'tab'], action: 'next-agent' },
  { keys: ['cmd', 'shift', 'tab'], action: 'previous-agent' },
  
  // UI controls
  { keys: ['cmd', '\\'], action: 'toggle-sidebar' },
  { keys: ['cmd', '/'], action: 'show-help' },
  
  // Terminal specific
  { keys: ['cmd', 'k'], action: 'clear-terminal' },
  { keys: ['cmd', 'f'], action: 'find-in-terminal' },
];
```

## Dependencies on Other Tasks
- Task 03: UI Components (for help overlay)
- Task 04: Agent Management (for agent actions)
- Task 07: Session Persistence (for saving custom shortcuts)

## Subtasks

### 8.1 Shortcut Manager Core (4 hours)
- Create ShortcutManager class
- Implement key event handling
- Build key combination parser
- Handle modifier keys properly
- Implement action dispatch system

### 8.2 Platform Detection (2 hours)
- Detect operating system
- Map Cmd to Ctrl on Linux
- Handle platform-specific keys
- Test on both platforms
- Create platform abstraction

### 8.3 Default Shortcuts (3 hours)
- Implement agent switching shortcuts
- Add terminal management shortcuts
- Create navigation shortcuts
- Test all default shortcuts
- Document shortcut behavior

### 8.4 Global Key Handling (3 hours)
- Set up global key listener
- Prevent browser defaults
- Handle focus management
- Implement key event bubbling
- Test in different contexts

### 8.5 Shortcut Customization (4 hours)
- Create customization interface
- Implement shortcut editor
- Add conflict detection
- Validate key combinations
- Save custom shortcuts

### 8.6 Help Overlay (3 hours)
- Design help overlay UI
- Group shortcuts by category
- Show current key bindings
- Add search functionality
- Implement dismiss actions

### 8.7 Visual Feedback (2 hours)
- Add shortcut hints in UI
- Show active shortcuts
- Implement key press indicators
- Add tooltip shortcuts
- Create status bar hints

### 8.8 Persistence Integration (2 hours)
- Save custom shortcuts
- Load shortcuts on startup
- Handle invalid shortcuts
- Migrate shortcut formats
- Export/import shortcuts

## Success Metrics
- Shortcut response time < 50ms
- Zero conflicts in default shortcuts
- All shortcuts discoverable via help
- Customization UI intuitive to use
- Works consistently across platforms

## UI/UX Considerations

### Help Overlay Design
```
┌─────────────────────────────────────┐
│        Keyboard Shortcuts           │
├─────────────────────────────────────┤
│ Agent Control                       │
│   Switch to Agent 1    ⌘1          │
│   Switch to Agent 2    ⌘2          │
│   Next Agent          ⌘Tab         │
│                                     │
│ Terminal                            │
│   New Terminal        ⌘T           │
│   Clear Terminal      ⌘K           │
│                                     │
│ [Customize Shortcuts]               │
└─────────────────────────────────────┘
```

### Shortcut Hints
- Show in tooltips
- Display in menu items
- Add to button labels
- Include in status bar

## Technical Challenges

### Challenge 1: Key Event Conflicts
- **Issue**: OS and browser shortcuts interference
- **Solution**: Careful event handling, preventDefault when appropriate

### Challenge 2: Cross-Platform Compatibility
- **Issue**: Different modifier keys and conventions
- **Solution**: Platform abstraction layer, testing

### Challenge 3: Terminal Focus
- **Issue**: Terminal consuming all key events
- **Solution**: Implement proper focus management

## Accessibility Features
- Ensure all actions available via keyboard
- Screen reader announces shortcuts
- High contrast mode for help overlay
- Customizable for accessibility needs
- Alternative key options

## Testing Requirements
- Unit tests for shortcut parsing
- Integration tests for key handling
- Cross-platform testing
- Accessibility testing
- Performance tests for key handling

## Customization UI
```typescript
interface ShortcutEditor {
  currentShortcuts: Shortcut[];
  editingShortcut: Shortcut | null;
  recordedKeys: string[];
  conflicts: string[];
}

// Recording new shortcut
// 1. Click on shortcut to edit
// 2. Press new key combination
// 3. Check for conflicts
// 4. Save or warn about conflict
```

## Risk Mitigation
- **Risk**: Platform-specific bugs
  - **Mitigation**: Extensive platform testing
- **Risk**: Shortcut conflicts
  - **Mitigation**: Comprehensive conflict detection
- **Risk**: Breaking terminal functionality
  - **Mitigation**: Careful focus management

## Future Enhancements
- Shortcut macros/sequences
- Context-aware shortcuts
- Vim-style command mode
- Shortcut profiles
- Cloud sync for shortcuts

## Notes
- Consider vim key bindings option
- Plan for international keyboards
- Document all shortcuts clearly
- Add shortcut cheat sheet export
- Consider gamepad support for accessibility