# Task 06: Git Integration - Project/Branch Detection

## Task Description
Implement automatic detection of git repository information including project name and current branch. This provides context in the agent table to help users identify which codebase each agent is working on. The system should gracefully handle non-git directories and update information when directories change.

## Priority: P0 (Must Have)
## Estimated Effort: 2-3 days

## Acceptance Criteria
- [ ] Detect if working directory is a git repository
- [ ] Extract repository name from git remote or folder name
- [ ] Detect current git branch accurately
- [ ] Update information when agent changes directories
- [ ] Handle non-git directories gracefully (show folder name)
- [ ] Cache git information for performance
- [ ] Support bare repositories and worktrees
- [ ] Handle detached HEAD states appropriately
- [ ] Work with submodules correctly
- [ ] Update branch info after git operations

## Technical Implementation Notes

### Git Detection Architecture
```typescript
interface GitInfo {
  isGitRepo: boolean;
  projectName: string;
  branch: string | null;
  remoteUrl?: string;
  isDetached: boolean;
  isDirty: boolean;
}

class GitDetector {
  private cache: Map<string, CachedGitInfo>;
  
  async getGitInfo(directory: string): Promise<GitInfo>;
  async watchDirectory(directory: string, callback: (info: GitInfo) => void): void;
  invalidateCache(directory: string): void;
}
```

### Detection Methods
1. **Git Command Execution**
   - Use `git` CLI for maximum compatibility
   - Parse command output carefully
   - Handle errors gracefully

2. **File System Watching**
   - Watch `.git/HEAD` for branch changes
   - Watch `.git/config` for remote changes
   - Implement intelligent cache invalidation

## Dependencies on Other Tasks
- Task 04: Agent Management (provides working directory)
- Updates: Task 03 (UI Components) with project/branch info

## Subtasks

### 6.1 Git Command Wrapper (3 hours)
- Create safe git command executor
- Handle command timeouts
- Parse git output formats
- Handle different git versions
- Implement error handling

### 6.2 Repository Detection (3 hours)
- Check for `.git` directory
- Handle `git rev-parse --show-toplevel`
- Support git worktrees
- Detect bare repositories
- Handle nested repositories

### 6.3 Project Name Extraction (4 hours)
- Parse git remote URLs
- Extract project name from URLs
- Handle multiple remotes
- Fallback to directory name
- Clean up project names

### 6.4 Branch Detection (4 hours)
- Use `git branch --show-current`
- Handle detached HEAD states
- Show commit hash when detached
- Handle empty repositories
- Support symbolic refs

### 6.5 Directory Monitoring (3 hours)
- Detect `cd` commands in terminal
- Update git info on directory change
- Handle symlinks properly
- Validate directory existence
- Batch updates for performance

### 6.6 Caching System (3 hours)
- Implement LRU cache for git info
- Set appropriate TTL values
- Invalidate on git operations
- Handle cache size limits
- Add cache statistics

### 6.7 File System Watching (2 hours)
- Watch `.git` directory changes
- Detect branch switches
- Monitor git operations
- Implement debouncing
- Handle watch errors

### 6.8 Non-Git Directory Handling (2 hours)
- Extract folder name nicely
- Handle special directories
- Provide clear indicators
- Show full path in tooltip
- Handle root directories

## Success Metrics
- Git detection completes in < 100ms (cached)
- First detection < 500ms
- Branch updates detected within 1 second
- Zero false git repository detections
- Cache hit rate > 90%

## Git Command Examples

### Repository Detection
```bash
# Check if directory is a git repo
git rev-parse --git-dir

# Get repository root
git rev-parse --show-toplevel

# Check if in worktree
git rev-parse --is-inside-work-tree
```

### Branch Information
```bash
# Current branch name
git branch --show-current

# For older git versions
git rev-parse --abbrev-ref HEAD

# Detached HEAD detection
git symbolic-ref -q HEAD
```

### Remote Information
```bash
# Get remote URL
git config --get remote.origin.url

# List all remotes
git remote -v

# Get default branch
git symbolic-ref refs/remotes/origin/HEAD
```

## Edge Cases

### Case 1: Detached HEAD
```typescript
// Show commit hash instead of branch
{
  branch: "HEAD detached at abc123f",
  isDetached: true
}
```

### Case 2: No Remote
```typescript
// Use directory name
{
  projectName: "local-project",
  remoteUrl: undefined
}
```

### Case 3: Multiple Remotes
```typescript
// Prefer 'origin', then first remote
const remotes = ['origin', 'upstream', 'fork'];
const selectedRemote = remotes.find(r => gitRemotes.includes(r)) || gitRemotes[0];
```

## Performance Optimizations

### Caching Strategy
```typescript
interface CacheEntry {
  info: GitInfo;
  timestamp: number;
  hits: number;
}

const CACHE_TTL = 5000; // 5 seconds
const CACHE_SIZE = 100; // Maximum entries
```

### Batch Operations
- Group multiple git queries
- Share git process when possible
- Implement request coalescing

## Testing Requirements
- Unit tests for git command parsing
- Integration tests with real git repos
- Test various git configurations
- Performance tests with large repos
- Edge case testing

## Risk Mitigation
- **Risk**: Git command not available
  - **Mitigation**: Check git availability on startup
- **Risk**: Slow git operations on large repos
  - **Mitigation**: Implement timeouts, aggressive caching
- **Risk**: Git output format changes
  - **Mitigation**: Handle multiple output formats

## Future Enhancements
- Show commit count ahead/behind
- Display dirty working directory indicator
- Integration with git status
- Show last commit message
- Support for other VCS (SVN, Mercurial)

## Notes
- Consider using nodegit for better performance
- Cache invalidation is critical for accuracy
- Handle git commands failing gracefully
- Consider showing git status indicators
- Plan for monorepo support