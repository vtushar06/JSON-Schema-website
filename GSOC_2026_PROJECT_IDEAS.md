# GSoC 2026 Project Ideas for JSON Schema

Based on comprehensive analysis of the JSON Schema ecosystem repositories, open issues, community discussions, and prior GSoC work, the following project ideas address real, existing problems and are scoped appropriately for a 10-22 week GSoC engagement.

---

## Project Idea 1: JSON Schema Tour - Content Expansion, Gamification & Accessibility

### Problem Statement

The [JSON Schema Tour](https://tour.json-schema.org) was initiated during GSoC 2024 and has made significant progress, but it remains incomplete and underutilized. Currently:

- **Content gaps**: Many planned lessons are missing or incomplete (issues #61, #65, #70, #167)
- **No gamification**: Users lack motivation to complete the tour; there's no progress tracking, achievements, or engagement mechanics (issue #78)
- **Poor mobile experience**: The tour page breaks on mobile devices with layout issues (issues #138, #209)
- **Limited accessibility**: No keyboard navigation support for users who can't use a mouse (issue #208)
- **No search functionality**: Users can't search for specific topics (issue #164)
- **No progress persistence**: Users lose their progress when they return (issue #202)

The Tour has 23 open issues and represents a valuable but underutilized educational resource that could significantly lower the barrier to JSON Schema adoption.

### Why This Matters to JSON Schema

JSON Schema has a steep learning curve. While the specification is comprehensive, newcomers often struggle to understand how to apply JSON Schema practically. The Tour was created to address this, but its incomplete state limits its effectiveness.

A polished, engaging, and accessible Tour would:
- **Reduce community support burden** by providing self-service learning
- **Increase adoption** by making JSON Schema more approachable
- **Serve as a showcase** for JSON Schema capabilities
- **Provide measurable learning outcomes** for organizations evaluating JSON Schema

### Proposed Solution / Architecture

**Phase 1: Content Expansion (Weeks 1-6)**
- Audit existing lessons and identify all content gaps
- Write 8-12 new lessons covering advanced topics:
  - Schema composition (`allOf`, `anyOf`, `oneOf`, `not`)
  - References and `$ref`
  - Conditional schemas (`if`/`then`/`else`)
  - String formats and custom formats
  - Schema bundling and reuse
- Implement proper test cases for each lesson
- Add MDX-based interactive examples with live validation

**Phase 2: Gamification & Progress Tracking (Weeks 7-10)**
- Implement persistent progress tracking using localStorage and optional account sync
- Create a progress dashboard showing completion percentage per section
- Add achievement badges for milestones (e.g., "Completed Basics", "Reference Master")
- Implement a streak system for daily practice
- Design certificate generation for course completion (integration with tour.json-schema.org/certificate)

**Phase 3: Mobile & Accessibility (Weeks 11-14)**
- Fix mobile layout issues (responsive design overhaul)
- Implement comprehensive keyboard navigation (Tab, Enter, Escape)
- Add ARIA labels and screen reader support
- Implement search functionality with fuzzy matching
- Add code beautify/format button for the editor

**Phase 4: Polish & Documentation (Weeks 15-17)**
- Comprehensive testing (unit tests with Vitest, E2E tests)
- Performance optimization
- Write contributor documentation for future content additions
- User testing and feedback incorporation

### Deliverables

| Milestone | Deliverable | Week |
|-----------|-------------|------|
| M1 | Content audit document + 4 new lessons published | 3 |
| M2 | 8+ new lessons covering advanced topics | 6 |
| M3 | Progress tracking system with dashboard | 8 |
| M4 | Achievement badge system with 6+ badges | 10 |
| M5 | Mobile-responsive design (passes WCAG AA) | 12 |
| M6 | Keyboard navigation + screen reader support | 14 |
| M7 | Search functionality | 15 |
| M8 | Full test coverage + documentation | 17 |

### Skills Required

- **Frontend Development**: React, Next.js, TypeScript (primary tech stack)
- **MDX**: Writing interactive documentation
- **CSS/Tailwind**: Responsive design implementation
- **Accessibility**: WCAG guidelines, ARIA attributes, keyboard navigation
- **JSON Schema**: Understanding of JSON Schema concepts (or willingness to learn)
- **Testing**: Vitest, React Testing Library

### Expected Difficulty

**Medium** - The project has a clear codebase with established patterns. The main challenges are creating quality educational content and implementing accessibility features correctly.

### Estimated Timeline

- **Community Bonding (3 weeks)**: Study existing codebase, understand MDX content structure, audit current lessons, create detailed content plan
- **Coding Phase 1 (6 weeks)**: Content expansion
- **Coding Phase 2 (4 weeks)**: Gamification and progress tracking
- **Coding Phase 3 (4 weeks)**: Mobile, accessibility, and polish
- **Buffer (1 week)**: Final testing and documentation

**Total: 18 weeks (Large project, 350 hours)**

### Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Content quality issues | Medium | High | Early mentor review of 2 lessons before proceeding |
| Scope creep on gamification | Medium | Medium | Define MVP feature set upfront; badges and progress are priority |
| Accessibility testing gaps | Low | High | Use automated tools (axe-core) + manual testing with screen readers |
| Mobile design complexity | Medium | Medium | Start with existing Tailwind patterns; iterate |

### Why This is GSoC-Suitable

- **Not too big**: The Tour exists and works; this is enhancement, not creation
- **Not too small**: 23 open issues + significant new content = substantial work
- **Clear milestones**: Each phase produces shippable, testable increments
- **Real impact**: Directly improves JSON Schema learning for thousands of developers
- **Mentorship opportunity**: Mix of writing, coding, and design work
- **Builds on prior work**: Continues momentum from GSoC 2024

### References

- Repository: https://github.com/json-schema-org/tour
- Live site: https://tour.json-schema.org
- Open issues: https://github.com/json-schema-org/tour/issues
- Original GSoC proposal: https://github.com/json-schema-org/community/issues/645

---

## Project Idea 2: JSON Schema Tooling Page - UX Overhaul & Enhanced Discovery

### Problem Statement

The JSON Schema website's [Tools page](https://json-schema.org/tools) is the primary resource for discovering JSON Schema implementations across languages and use cases. With **100+ tools** listed, it has become difficult to navigate effectively. Current pain points include:

- **No deep linking**: Users cannot share links to specific tools; refreshing the page loses modal state (issue #2034)
- **Filter state not persisted**: "Show Obsolete" and "Supports Bowtie" filters break on refresh (issue #2033)
- **Poor filter accessibility**: "Apply Filters" button gets buried in long dropdown menus (issue #2086)
- **No sticky sidebar**: Users must scroll back up to change filters (issue #2105)
- **Missing "Jump to Section" navigation**: When tools are grouped, there's no quick way to navigate to a specific group (issue #2104)
- **No pagination**: Loading all tools at once causes performance issues; no page-based navigation (issue #2094)
- **Missing language metadata**: Some tools don't appear when filtering by language due to undefined metadata (issue #2153)
- **Mobile layout issues**: Heading, spacing, and controls need cleanup for mobile (issue #1989)
- **Brief rendering flash**: Default grouping briefly displays before applying saved preferences (issue #2140)

These issues significantly degrade the developer experience when trying to find the right JSON Schema tool.

### Why This Matters to JSON Schema

The Tools page is one of the most visited pages on json-schema.org and serves as the primary discovery mechanism for the JSON Schema ecosystem. Poor UX means:

- **Developers can't find suitable tools** for their use case
- **Tool maintainers don't get visibility** for their implementations
- **Bowtie compliance data isn't discoverable**, undermining ecosystem quality efforts
- **Mobile users are effectively excluded** from the tool discovery experience

A polished tooling page would strengthen the entire JSON Schema ecosystem by connecting developers with the right implementations.

### Proposed Solution / Architecture

**Phase 1: URL State Management & Deep Linking (Weeks 1-4)**
- Implement URL-based state management for filters, sort order, and grouping using `next/router` query parameters
- Add hash-based deep linking to tool modals (e.g., `/tools#ajv`)
- Create robust slugification logic for tool names with special characters
- Ensure browser back/forward navigation works correctly
- Persist user preferences to localStorage with URL taking precedence

**Phase 2: Enhanced Navigation & Filtering (Weeks 5-9)**
- Implement sticky filter sidebar that remains visible while scrolling
- Add "Jump to Section" quick navigation when tools are grouped
- Move "Apply Filters" button to a fixed position (always visible)
- Fix filter state persistence bugs
- Add filter chips/tags showing active filters with one-click removal
- Implement keyboard shortcuts for common filter operations

**Phase 3: Performance & Pagination (Weeks 10-13)**
- Implement server-side pagination or virtual scrolling for large lists
- Add page numbers and "jump to page" functionality
- Optimize initial render to prevent flash of default state
- Implement loading skeletons for better perceived performance
- Add "Load More" progressive loading as an alternative to pagination

**Phase 4: Mobile Experience & Polish (Weeks 14-17)**
- Complete mobile layout redesign with proper touch targets
- Implement mobile-specific filter interface (bottom sheet or expandable panel)
- Fix all mobile-specific bugs
- Add responsive table design that works on small screens
- Comprehensive testing across device sizes

### Deliverables

| Milestone | Deliverable | Week |
|-----------|-------------|------|
| M1 | URL state management for filters and sorting | 2 |
| M2 | Deep linking to tool modals + browser navigation | 4 |
| M3 | Sticky sidebar implementation | 6 |
| M4 | Jump-to-section navigation | 7 |
| M5 | Filter UX improvements (chips, keyboard shortcuts) | 9 |
| M6 | Pagination/virtual scrolling | 11 |
| M7 | Performance optimizations | 13 |
| M8 | Mobile layout overhaul | 15 |
| M9 | Testing + documentation | 17 |

### Skills Required

- **React & Next.js**: Primary framework (current stack)
- **TypeScript**: All code must be type-safe
- **Tailwind CSS**: Styling approach used throughout
- **State Management**: URL state, localStorage, React hooks
- **Performance Optimization**: Virtual scrolling, lazy loading, React profiling
- **Responsive Design**: Mobile-first approach, touch interactions
- **Testing**: Cypress (existing test framework)

### Expected Difficulty

**Medium** - The tooling page already exists with clear architecture. The challenge is implementing proper state management and improving UX without breaking existing functionality. All patterns exist in the codebase; this is enhancement work.

### Estimated Timeline

- **Community Bonding (3 weeks)**: Study existing tooling page code (`pages/tools/`), understand data flow, audit all related issues, create technical design document
- **Coding Phase 1 (4 weeks)**: URL state management and deep linking
- **Coding Phase 2 (5 weeks)**: Navigation and filtering improvements
- **Coding Phase 3 (4 weeks)**: Performance and pagination
- **Coding Phase 4 (4 weeks)**: Mobile and polish
- **Buffer (2 weeks)**: Testing, bug fixes, documentation

**Total: 22 weeks (Large project, 350 hours)**

### Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Breaking existing functionality | Medium | High | Comprehensive test coverage before changes; feature flags for gradual rollout |
| URL state becoming complex | Medium | Medium | Use established library (nuqs or similar) for URL state management |
| Performance regressions | Low | High | Establish performance benchmarks; test with full dataset |
| Mobile design scope creep | Medium | Medium | Define specific breakpoints and behaviors upfront |

### Why This is GSoC-Suitable

- **Not too big**: Scoped to one page with clear boundaries
- **Not too small**: 12+ related issues covering multiple UX improvements
- **Incremental delivery**: Each phase produces independently valuable improvements
- **Real user impact**: Directly improves tool discovery for all JSON Schema users
- **Clear technical path**: Uses existing tech stack; no new technologies needed
- **Measurable success**: Can track user engagement metrics before/after

### References

- Tooling page: https://json-schema.org/tools
- Related issues:
  - Deep linking: https://github.com/json-schema-org/website/issues/2034
  - Sticky sidebar: https://github.com/json-schema-org/website/issues/2105
  - Pagination: https://github.com/json-schema-org/website/issues/2094
  - Mobile layout: https://github.com/json-schema-org/website/issues/1989
  - Filter bugs: https://github.com/json-schema-org/website/issues/2033
- Current implementation: `pages/tools/` directory in website repo

---

## Summary Comparison

| Aspect | Tour Enhancement | Tooling Page Overhaul |
|--------|------------------|----------------------|
| **Primary Impact** | Learning & onboarding | Tool discovery |
| **Tech Stack** | Next.js, MDX, React | Next.js, React, Tailwind |
| **Content vs Code** | 40% content, 60% code | 10% content, 90% code |
| **Existing Issues** | 23 open issues | 12+ related issues |
| **Visibility** | tour.json-schema.org | json-schema.org/tools |
| **Prior GSoC Work** | Yes (2024) | No |
| **Difficulty** | Medium | Medium |
| **Timeline** | 18 weeks | 22 weeks |

Both projects address real, documented problems with clear community interest and would provide meaningful improvements to the JSON Schema ecosystem.

---

## Recommendation

**For GSoC 2026, I recommend prioritizing Project Idea 1 (Tour Enhancement)** because:

1. **Continues proven investment**: The Tour already received GSoC funding; completing it maximizes prior investment
2. **Higher educational impact**: A complete Tour benefits every new JSON Schema user
3. **More diverse work**: Mix of content creation, coding, and design provides richer learning experience
4. **Clear community interest**: The original proposal had 35+ comments from interested contributors
5. **Lower risk**: Existing codebase with established patterns reduces technical uncertainty

**Project Idea 2 (Tooling Page)** is equally valid and could be proposed if mentors prefer a more code-focused project or if Tour mentors are unavailable.

---

*Document prepared for GSoC 2026 project idea submission. Based on analysis conducted January 2026.*
