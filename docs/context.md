# Handz Project Context

## Project Overview
**Project Name:** Handz

## Current Phase
- PRD consolidation and cleanup
- **No app code should be generated yet**

## Technical Decisions Made

### Platform
- **iOS first**, Android later
- No web app for v1

### Technology Stack
- **Frontend:** React Native with Expo
- **Backend:** Firebase
- **Database:** Firestore
- **State Management:** Zustand
- **AI Provider:** Claude (Anthropic) via OpenCode

## Constraints

### PRD Management
- All existing files in this directory are PRDs and **must remain untouched**
- PRDs are PDFs and exceed the 100-page ingestion limit
- PRDs must be processed in small batches

## PRD Processing Workflow

### Batch Processing Rules
1. Read PRDs in batches (variable size based on complexity, ~4 chapters default)
2. Extract key requirements per batch
3. Identify ambiguities/placeholders
4. Save results to `docs/prd_batch_XX.md`
5. **Get approval before moving to next batch**

### Batch Sizing Strategy
- **Light chapters** (definitions, checklists): 5-6 chapters
- **Dense chapters** (Flow Builder, Practice Mode, Mastery): 2-3 chapters
- **System chapters** (Auth, Moves, Sharing): 3-4 chapters

### Extraction Format
Each batch markdown file includes:
- Brief chapter overview (1-2 paragraphs)
- Key requirements (bullet points, grouped by feature/system)
- Open questions / ambiguities / placeholders
- Cross-references to other chapters
- Explicit assumptions detected

### Ambiguity Flagging Criteria
Flag all of the following:
- Explicit "TBD", "TBC", or placeholder markers
- Vague/non-committal language ("should", "may", "could", "might")
- Missing specifications where decisions are implied but not defined
- Conflicting statements within or across chapters

### Detail Level
- Include user-facing requirements (when stated)
- Include technical/backend requirements (when explicitly stated)
- Capture system-level and architectural decisions (when mentioned)
- **Do NOT invent or infer missing technical details**

## Important Notes
- **Do not generate application code**
- Focus on understanding and documenting requirements
- Maintain traceability between PRD chapters and extracted requirements
- Stop after each batch and wait for approval

## PRD Inventory (39 Chapters)

### Foundation & Scope (CH00-CH04)
- CH00: Master Index and Manifest
- CH01: Product Definition and V1 Scope
- CH02: Non-Goals, Assumptions, Constraints
- CH03: Core Concepts and Glossary
- CH04: Information Architecture and Navigation Map

### Design & Authentication (CH05-CH08)
- CH05: Screen Inventory
- CH06: Design System
- CH07: Authentication & Account System
- CH08: Entitlements and Plan States

### Move Library System (CH09-CH11)
- CH09: Default Move Library System
- CH10: Moves - Canonical IDs, Aliases, Families, Variants
- CH11: Moves - Custom Moves, Editing, Revert Model

### Flow Builder (CH12-CH16)
- CH12: Flow Builder Interaction Model
- CH13: Flow Builder Node Types and Data Model
- CH14: Sequence Detail Editor
- CH15: Library - Flows, Folders, Search, Sort
- CH16: Flow Detail View

### Sharing & Collaboration (CH17-CH19)
- CH17: Sharing - Unlisted Links, Link Lifecycle
- CH18: Inbox - Receiving, Imports
- CH19: Import Conflict Resolution

### Practice Mode (CH20-CH22)
- CH20: Practice Mode Setup
- CH21: Practice Mode Active Session
- CH22: Practice Mode Logging and History

### Progress & Monetization (CH23-CH26)
- CH23: Mastery and Gameplans
- CH24: Maintenance System
- CH25: Monetization - Paywalls, Upsells
- CH26: Scientific Claims & Education Pages

### System Features (CH27-CH32)
- CH27: Notifications
- CH28: Offline Behavior and Sync
- CH29: Data Storage and Limits
- CH30: Safety, Abuse, Limits, Warning Ladder
- CH31: Error States
- CH32: Accessibility and Localization Readiness

### Analytics & Data Management (CH33-CH34)
- CH33: Analytics and Metrics Spec
- CH34: Data Export & Account Deletion

### QA & Deployment (CH35-CH38)
- CH35: QA Acceptance Tests
- CH36: Troubleshooting Playbook
- CH37: Vibe Coding Prompt Pack
- CH38: Development Milestones & Release Checklist
