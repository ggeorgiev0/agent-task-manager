# Product Requirements Document: Agent Task Manager

## 1. Executive Summary

Agent Task Manager is a desktop application that serves as a multi-instance wrapper for Claude Code, enabling developers to efficiently manage multiple AI coding agents simultaneously. The application provides a unified interface to monitor and interact with up to 5+ Claude Code instances running in different projects and branches, with instant visibility of which agents require user attention.

### Key Value Proposition
- Eliminates the need to constantly switch between terminal windows to check agent status
- Provides instant visual feedback when any agent requires user action
- Maintains persistent sessions across application restarts
- Streamlines multi-project development workflows

## 2. Problem Statement

Developers working on multiple codebases simultaneously face significant context-switching overhead when using Claude Code. Currently, they must:
- Manually switch between multiple terminal windows to check if agents need input
- Lose track of which agents are actively working vs. waiting for approval
- Lack a consolidated view of all running agents and their current states
- Experience workflow interruptions due to missed agent prompts

This results in:
- Reduced productivity due to constant window switching
- Delayed responses to agent requests
- Difficulty managing parallel development tasks
- Mental overhead tracking multiple agent states

## 3. Goals & Success Metrics

### Primary Objectives
1. Reduce time to respond to agent requests by 80%
2. Enable efficient management of 5+ concurrent Claude Code instances
3. Provide instant visual feedback for agent status changes
4. Maintain zero data loss across application restarts

### Success Metrics
- **Response Time**: Average time from "REQUIRES ACTION" state to user response < 30 seconds
- **Uptime**: Application stability with < 1% crash rate
- **User Efficiency**: 90% reduction in window switching during multi-agent workflows
- **Session Persistence**: 100% successful session restoration on app restart

## 4. User Personas & Stories

### Primary Persona: Senior Developer
- **Name**: Alex (based on user profile)
- **Role**: Senior Backend Developer
- **Technical Skills**: Expert in NodeJS and TypeScript
- **Work Pattern**: Manages 3-5 simultaneous projects
- **Pain Points**: Constant context switching, missed agent prompts
- **Goals**: Maximize productivity, minimize interruptions

### User Stories

**US-001**: As a developer, I want to see all my running Claude Code agents in one place, so I can quickly identify which needs attention.
- **Acceptance Criteria**:
  - Table displays all active agents with project, branch, and status
  - Status updates in real-time
  - Visual distinction between different states

**US-002**: As a developer, I want to switch between agents with a single click, so I can quickly respond to requests.
- **Acceptance Criteria**:
  - Click on table row switches terminal view
  - Terminal history preserved for each agent
  - Immediate visual feedback on selection

**US-003**: As a developer, I want clear visual indicators when an agent needs input, so I never miss a prompt.
- **Acceptance Criteria**:
  - "REQUIRES ACTION" shown with red background
  - Status visible regardless of which agent is active
  - No audio/popup notifications needed

**US-004**: As a developer, I want my sessions to persist across app restarts, so I can resume work seamlessly.
- **Acceptance Criteria**:
  - Working directories saved on exit
  - Directories reopened on app launch
  - No agent auto-start (manual control retained)

**US-005**: As a developer, I want to add new agent terminals on demand, so I can start new tasks as needed.
- **Acceptance Criteria**:
  - "Add Terminal" button creates new session
  - New terminals open in user's home directory
  - Automatic focus switch to new terminal

**US-006**: As a developer, I want keyboard shortcuts for common actions, so I can work efficiently.
- **Acceptance Criteria**:
  - Default shortcuts for switching agents (e.g., Cmd+1, Cmd+2)
  - Customizable shortcut configuration
  - Shortcuts work globally within app

## 5. Feature Requirements

### Core Features (P0 - Must Have)

**F-001: Multi-Terminal Management**
- Embed multiple terminal instances
- Each terminal runs independent Claude Code session
- Terminal output buffering and persistence
- Smooth switching between terminals

**F-002: Agent Status Table**
- Columns: Project, Branch, Status
- Real-time status updates
- Click-to-focus functionality
- Vertically resizable table area

**F-003: Status Detection System**
- Parse Claude Code output for action prompts
- Detect three states: WORKING, REQUIRES ACTION, ERROR
- Visual status indicators:
  - WORKING: Normal text with pulsing animation (every 1s or on new output)
  - REQUIRES ACTION: Red background
  - ERROR: Distinct visual treatment

**F-004: Project/Branch Detection**
- Automatic git repository detection
- Parse current branch from git info
- Use working directory name as fallback
- Update on directory changes

**F-005: Session Persistence**
- Save working directories on app close
- Restore directories on app open
- No automatic agent restart
- Preserve window size/position

**F-006: Terminal Controls**
- "Add Terminal" button
- Terminal opens in user home directory
- Automatic focus on new terminal
- No terminal closing/removal in v1

### Future Considerations (P1 - Nice to Have)
- Custom agent labels/names
- Agent filtering and sorting
- Terminal color themes
- Export/import session configurations
- Agent resource usage monitoring

## 6. Technical Specifications

### Technology Stack
- **Framework**: Electron (or modern alternative like Tauri)
- **Frontend**: React/Vue/Svelte with TypeScript
- **Terminal Emulation**: xterm.js or node-pty
- **State Management**: Redux/Zustand/Pinia
- **Build System**: Vite/Webpack
- **Platform Support**: macOS and Linux

### Architecture Overview
```
┌─────────────────────────────────────────┐
│          Electron Main Process          │
│  - Window Management                    │
│  - Process Spawning                     │
│  - File System Access                   │
└────────────────────┬────────────────────┘
                     │
┌────────────────────┴────────────────────┐
│         Electron Renderer Process       │
│  ┌──────────────────────────────────┐  │
│  │        Terminal Component        │  │
│  │  - xterm.js instances            │  │
│  │  - PTY communication             │  │
│  └──────────────────────────────────┘  │
│  ┌──────────────────────────────────┐  │
│  │      Agent Table Component       │  │
│  │  - Status monitoring             │  │
│  │  - Click handling                │  │
│  └──────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

### Integration Requirements
- **Claude Code CLI**: Spawn as child process
- **Git**: Command-line git for branch detection
- **File System**: Read/write for session persistence
- **OS Integration**: Native window management

### Data Requirements
- **Session State**: JSON file in app data directory
- **Terminal Buffers**: In-memory with configurable limits
- **Configuration**: User preferences in standard config location

### Performance Requirements
- **Startup Time**: < 2 seconds
- **Terminal Switching**: < 100ms
- **Status Update Latency**: < 500ms
- **Memory Usage**: < 500MB with 5 active agents

## 7. User Experience Guidelines

### Design Principles
1. **Minimal and Focused**: Clean interface without unnecessary features
2. **Information Density**: Maximum terminal space, minimal chrome
3. **Visual Clarity**: Clear status indicators, obvious active states
4. **Keyboard-First**: Full keyboard navigation support

### Layout Specifications
- **Terminal Area**: 70-80% of window height
- **Agent Table**: 20-30% of window height
- **Resizable Divider**: Between terminal and table
- **No Sidebars**: Maximize horizontal space for terminal

### Visual Design
- **Color Scheme**: Match system dark/light mode
- **Typography**: Monospace for terminal, system font for UI
- **Status Colors**:
  - Working: Default text
  - Requires Action: Red background (#dc2626)
  - Error: Orange/yellow indicator
- **Animation**: Subtle pulse for "WORKING" status

### Accessibility
- **Keyboard Navigation**: Full app control via keyboard
- **Screen Reader**: Basic support for agent table
- **High Contrast**: Respect system accessibility settings
- **Focus Indicators**: Clear visual focus states

## 8. Implementation Plan

### Phase 1: Foundation (Week 1-2)
1. Set up Electron/Tauri project structure
2. Implement basic terminal embedding
3. Create agent table component
4. Basic terminal switching

### Phase 2: Core Features (Week 3-4)
1. Claude Code process spawning
2. Output parsing and status detection
3. Git integration for project/branch info
4. Session persistence

### Phase 3: Polish (Week 5-6)
1. Keyboard shortcuts
2. Visual polish and animations
3. Error handling and edge cases
4. Performance optimization

### Phase 4: Testing & Release (Week 7-8)
1. Cross-platform testing (macOS/Linux)
2. User acceptance testing
3. Documentation
4. Initial release

### Dependencies
- Claude Code CLI must be installed
- Git must be available in PATH
- macOS 10.15+ or Linux with X11/Wayland

### Risks
- **Technical**: Claude Code output parsing complexity
- **Performance**: Terminal emulation overhead with multiple instances
- **Compatibility**: Cross-platform terminal behavior differences

### Mitigation Strategies
- Research Claude Code output patterns early
- Implement output buffering and virtual scrolling
- Use established terminal emulation libraries

## 9. Out of Scope

### v1 Exclusions
- Windows support
- Cloud synchronization
- Agent auto-start/auto-resume
- Custom agent naming/labeling
- Advanced filtering/sorting
- Multiple windows/tabs
- Integrated diff viewer
- Direct file editing
- Agent performance metrics
- Team collaboration features
- Claude Code configuration management
- Automated testing of agents

### Future Version Considerations
- Windows platform support
- Plugin system for extensions
- Agent templates and presets
- Batch operations on multiple agents
- Integration with other AI coding tools

## 10. Appendices

### A. Technical Research Needed
1. Claude Code output format analysis
2. Best practices for Electron/Tauri + multiple terminals
3. Performance benchmarks for terminal emulators
4. Cross-platform keyboard shortcut handling

### B. Alternative Approaches Considered
1. **Web-based Solution**: Rejected due to terminal limitations
2. **VS Code Extension**: Rejected for independence from specific IDE
3. **Native Terminal Multiplexer**: Rejected for better GUI control

### C. Example Status Detection Patterns
```
# Potential Claude Code patterns to detect:
- "Do you want to proceed? (y/n)"
- "Please select an option:"
- "Waiting for user input..."
- "Permission required to:"
- Error outputs starting with "Error:" or "Failed:"
```

### D. Configuration File Structure
```json
{
  "version": "1.0.0",
  "sessions": [
    {
      "id": "uuid",
      "workingDirectory": "/path/to/project",
      "lastActive": "2024-01-01T00:00:00Z"
    }
  ],
  "shortcuts": {
    "switchAgent1": "Cmd+1",
    "switchAgent2": "Cmd+2",
    "addTerminal": "Cmd+T"
  },
  "ui": {
    "terminalHeightPercent": 75,
    "theme": "auto"
  }
}
```