# Agent Task Manager

A desktop application that manages multiple Claude Code AI agents in a unified interface, eliminating the need to switch between terminal windows and providing instant visibility of agent status.

## Overview

Agent Task Manager is designed for developers who work with multiple codebases simultaneously using Claude Code. It provides a single window to monitor and control up to 5+ AI coding agents, with clear visual indicators when any agent requires user attention.

### Key Features

- **Multi-Agent Management**: Run multiple Claude Code instances in separate terminals within one window
- **Status Monitoring**: Instant visibility of agent states (Working, Requires Action, Error)
- **One-Click Switching**: Switch between agents by clicking on the status table
- **Session Persistence**: Automatically saves and restores working directories
- **Keyboard Shortcuts**: Customizable hotkeys for efficient navigation
- **Visual Alerts**: Red background highlighting when agents need user input

## Problem Solved

When working with multiple Claude Code agents across different projects, developers face:
- Constant window switching to check agent status
- Missed prompts that block agent progress
- Loss of context when jumping between projects
- No unified view of all running agents

Agent Task Manager solves these issues by consolidating all agents into a single, efficient interface.

## Tech Stack

- **Desktop Framework**: Electron (or Tauri)
- **Frontend**: TypeScript + Modern JS Framework (React/Vue/Svelte)
- **Terminal Emulation**: xterm.js
- **Platform Support**: macOS and Linux

## Project Status

🚧 **In Development** - Currently in the planning and initial development phase.

## Documentation

- [Product Requirements Document](./prd-agent-task-manager.md) - Comprehensive requirements and specifications
- [Task Breakdown](./tasks/) - Development tasks broken down into manageable chunks

## Getting Started

*Coming soon - Build and installation instructions will be added as development progresses.*

## Contributing

This project is currently in initial development. Contribution guidelines will be added once the core architecture is established.