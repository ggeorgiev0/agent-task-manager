# Task 03: UI Components (Agent Table and Layout)

## Task Description
Design and implement the core UI components including the agent status table, application layout with resizable panels, and the visual design system. This establishes the user interface foundation that displays agent information and enables interaction.

## Priority: P0 (Must Have)
## Estimated Effort: 3-4 days

## Acceptance Criteria
- [ ] Agent table displays columns: Project, Branch, Status
- [ ] Table rows are clickable to switch terminals
- [ ] Visual feedback on row hover and selection
- [ ] Status column shows appropriate visual indicators
- [ ] Layout with terminal area (70-80%) and table area (20-30%)
- [ ] Resizable divider between terminal and table sections
- [ ] "Add Terminal" button in appropriate location
- [ ] Responsive layout that handles window resizing
- [ ] Dark/light theme support following system preferences
- [ ] Consistent design system with reusable components

## Technical Implementation Notes

### Component Architecture
```
App
├── Layout
│   ├── Header (minimal or none)
│   ├── MainContent
│   │   ├── TerminalSection
│   │   │   └── Terminal (from Task 02)
│   │   └── Divider (resizable)
│   └── AgentTableSection
│       ├── AgentTable
│       │   ├── TableHeader
│       │   └── TableRow[]
│       └── AddTerminalButton
```

### UI State Management
```typescript
interface UIState {
  agents: Agent[];
  activeAgentId: string | null;
  terminalHeightPercent: number;
  theme: 'light' | 'dark' | 'auto';
}

interface Agent {
  id: string;
  project: string;
  branch: string;
  status: 'working' | 'requires-action' | 'error';
  workingDirectory: string;
  lastOutput?: string;
}
```

### Design System
- Use CSS-in-JS (styled-components/emotion) or CSS modules
- Define design tokens for consistency
- Implement theme provider for dark/light modes
- Use system font stack for UI, monospace for terminals

## Dependencies on Other Tasks
- Task 01: Project Setup (must be complete)
- Works in parallel with: Task 02 (Terminal Integration)
- Provides UI for: Task 04 (Agent Management), Task 05 (Status Detection)

## Subtasks

### 3.1 Design System Setup (3 hours)
- Set up CSS-in-JS library or CSS modules
- Define color palette for light/dark themes
- Create typography scale and font definitions
- Define spacing system and layout constants
- Implement theme provider component

### 3.2 Layout Components (4 hours)
- Create main layout container with flexbox/grid
- Implement terminal section wrapper
- Implement agent table section wrapper
- Add proper overflow handling
- Ensure full height layout without scrollbars

### 3.3 Resizable Divider (4 hours)
- Implement draggable divider component
- Handle mouse events for resizing
- Persist divider position to state
- Add visual feedback during drag
- Implement min/max constraints (20-80%)

### 3.4 Agent Table Component (6 hours)
- Create table structure with proper semantics
- Implement table header with column labels
- Create table row component with hover states
- Add click handlers for row selection
- Implement active row highlighting
- Add empty state when no agents exist

### 3.5 Status Indicators (3 hours)
- Design status badge components
- Implement color coding for each status
- Add pulsing animation for "working" status
- Create consistent status styling
- Ensure accessibility with proper contrast

### 3.6 Add Terminal Button (2 hours)
- Design and position add button
- Implement click handler interface
- Add hover and active states
- Consider icon vs text label
- Ensure keyboard accessibility

### 3.7 Theme Implementation (3 hours)
- Detect system theme preference
- Implement theme switching logic
- Apply theme to all components
- Ensure smooth theme transitions
- Test contrast ratios for accessibility

### 3.8 Responsive Behavior (2 hours)
- Handle window resize events
- Implement minimum window size
- Test various window dimensions
- Ensure table remains usable at small sizes
- Handle text truncation appropriately

### 3.9 Component Testing (3 hours)
- Write unit tests for table components
- Test resize behavior
- Test theme switching
- Test keyboard navigation
- Add Storybook stories for components

## Success Metrics
- Table updates reflect status changes within 100ms
- Divider dragging is smooth at 60 FPS
- Theme switching completes in < 200ms
- All interactive elements have visible focus states
- Color contrast meets WCAG AA standards

## UI/UX Specifications

### Visual Hierarchy
1. Terminal content (primary focus)
2. Agent status indicators (secondary)
3. Project/branch info (tertiary)
4. UI controls (quaternary)

### Color Palette
```css
/* Light Theme */
--bg-primary: #ffffff;
--bg-secondary: #f3f4f6;
--text-primary: #111827;
--text-secondary: #6b7280;
--border: #e5e7eb;
--status-working: #3b82f6;
--status-action: #dc2626;
--status-error: #f59e0b;

/* Dark Theme */
--bg-primary: #111827;
--bg-secondary: #1f2937;
--text-primary: #f9fafb;
--text-secondary: #9ca3af;
--border: #374151;
/* Status colors remain similar */
```

### Interaction Patterns
- Single click: Select agent and switch terminal
- Hover: Highlight row with subtle background change
- Keyboard: Arrow keys navigate, Enter selects
- Focus: Clear outline for keyboard navigation

## Risk Mitigation
- **Risk**: Performance issues with frequent table updates
  - **Mitigation**: Use React.memo, virtualization if needed
- **Risk**: Resizer implementation complexity
  - **Mitigation**: Consider using react-resizable-panels
- **Risk**: Theme inconsistencies
  - **Mitigation**: Centralize theme definitions, use CSS variables

## Notes
- Keep UI minimal to maximize terminal space
- Avoid unnecessary animations that could distract
- Ensure all text is selectable for debugging
- Consider adding tooltips for truncated text
- Plan for future customization options