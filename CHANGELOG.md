# Changelog

All notable changes to Swift FocusEngine Pro are documented here.

## [1.7.1] - 2026-06-04

### Fixed
- **Repo structure — `npx skills add` now resolves correctly.** The repo previously had both a root `SKILL.md` and a duplicate `swift-focusengine-pro/SKILL.md` subfolder (same skill name). Installers stopped at the root `SKILL.md` and copied the whole repo, leaving `references/` one level too deep and breaking the skill's own reference paths. The skill is now a single flat skill at the repo root (`SKILL.md` + `references/` + `agents/`); the duplicate subfolder is removed and `package.json` points at `.`. No skill-content changes from 1.7.0 — this is purely a packaging fix.
- Root `SKILL.md` description upgraded to the fuller write/review trigger text for better auto-activation.

## [1.7.0] - 2026-06-04

### Improved diagnosis (no new anti-patterns)

These edits make existing fixes findable from the symptom — the fix for section re-entry was already documented in anti-pattern #25, but nothing routed you to it from the observed behavior.

- **`swiftui-focus.md` — `focusSection()` "No last-focused memory" gotcha.** Explicit statement that, unlike UIKit's `remembersLastFocusedIndexPath`, `focusSection()` picks geometrically on every entry and remembers nothing. Names the common symptom (arrowing Up from a grid into a row of pills lands on the nearest pill, not the selected one) and routes to anti-pattern #25 for the fix. Notes that a reactive `onChange` redirect causes a visible hop and is not the fix.
- **`swiftui-focus.md` — section-width escape gotcha.** A `focusSection()` narrower than (or offset from) the content below it leaves columns with no section overhead, so Up escapes past it (e.g. to the tab bar). Fix: `.frame(maxWidth: .infinity, alignment: .leading)` before `.focusSection()`.
- **`focus-restoration.md` — ZStack `if/else` overlay restoration.** A hand-rolled overlay swap does not auto-restore focus like `.sheet()`/`.fullScreenCover()`. Documents the timing trap: a synchronous `@FocusState` assignment on dismiss is dropped because the target isn't rebuilt yet — defer with `Task { @MainActor in … }`.

## [1.6.0] - 2026-04-29

### Added
- **New tvOS anti-pattern #30** — Missing `preferredFocusEnvironments` override on UIKit view controllers with multiple focusable subviews. Without an explicit override, tvOS picks the geometrically first focusable view, which often lands on a secondary CTA (e.g., "Back to Home") instead of the primary action (e.g., "Sign In").
- **Absence-check trigger** — pr-review-style guidance now flags vertical `UIStackView` of buttons, focusable list + standalone buttons, conditional CTA, and modal/sheet presentations that lack a `preferredFocusEnvironments` override. Absence of the override is itself a finding.
- **`uikit-focus.md`: "When to override `preferredFocusEnvironments`" section** — enumerates the trigger conditions and provides a conditional-CTA pattern with `setNeedsFocusUpdate()` cross-reference (anti-pattern #7) for state changes after the view appears.
- Total anti-patterns: 30 (up from 29)

## [1.5.0] - 2026-04-13

### Added
- **5 new production tvOS anti-patterns** (#25–29) from large-scale media-app tvOS development:
  - #25: `.disabled()` on multiple list items with active selection state — mass-toggle focus cascade
  - #26: `ScrollViewReader.scrollTo()` inside `onChange` creates feedback loops with focus engine
  - #27: `@Observable` same-value mutation triggers unnecessary body re-evaluation
  - #28: `defaultFocus` with `.userInitiated` only fires on initial appearance, not re-entry
  - #29: Transient focus bouncing during navigation transitions (sidebar pass-through)
- **Production sidebar pattern** — dual `@FocusState` (container + per-item) with `.disabled()` gating for focus re-entry
- **UIKit reference-codebase sidebar comparison** — `remembersLastFocusedIndexPath`, container-level `isUserInteractionEnabled`, 0.5s debounce
- **`ScrollPosition` vs `ScrollViewReader`** — declarative scroll binding that doesn't fight the focus engine
- **Scroll edge fade patterns** — `.scrollEdgeEffectStyle(.soft)` (tvOS 26+), manual gradient mask with `.mask()`, `onGeometryChange` tracking
- **Focus scale matching** — reference 1.13x scale comparison table for SwiftUI `scaleEffect`
- **`@Observable` focus integration** — same-value guard, `@ObservationIgnored` for non-UI state
- **ScrollTo feedback loop documentation** — detailed cause/fix in `async-focus.md`
- **Focus cascade debugging guide** — structured logging patterns, what to look for in cascade logs
- **VoiceOver scroll animation guard** — check `UIAccessibility.isVoiceOverRunning` before animated scroll
- **Updated anti-pattern #1** — added caveat about `.allowsHitTesting(false)` reliability on tvOS
- **Updated SKILL.md core instructions** — `defaultFocus` re-entry limitation, `ScrollPosition` preference
- Total anti-patterns: 29 (up from 24)

## [1.4.0] - 2026-04-10

### Added
- **3 new tvOS anti-patterns from production** (#15–17) — `LazyVStack` focus escape, vertical `.focusSection()`, allocation in focus callbacks
- **VStack + inner LazyHStack pattern** — lightweight outer container stays in hierarchy, heavy content stays lazy inside each row
- **Tab bar focus escape detection** — `didUpdateFocus` pattern for detecting when focus escapes content to tab bar
- **VoiceOver card composition pattern** — `.accessibilityElement(children: .ignore)` with composed labels for multi-element focusable cards
- Total anti-patterns: 24 (up from 21)

## [1.3.0] - 2026-04-10

### Added
- **macOS focus coverage** — new `macos-focus.md` reference file (650+ lines)
  - AppKit: NSResponder chain, `acceptsFirstResponder`, `canBecomeKeyView`, key view loop
  - Key window vs main window, NSPanel focus behavior, `becomesKeyOnlyIfNeeded`
  - Focus ring: `NSFocusRingType`, `drawFocusRingMask()`, custom shapes
  - SwiftUI on macOS: `@FocusState`, `.focusable()`, `.focusSection()`, `.onKeyPress`
  - `focusedValue` / `focusedSceneValue` for menu bar commands
  - NSToolbar, NSPopover, sheets, NSAlert focus
  - Multi-window, multi-screen, external display
  - Mac Catalyst bridging
  - Full Keyboard Access
- **7 macOS-specific anti-patterns** (#15–21) in `anti-patterns.md`
- macOS focus ring styling in `focus-styling.md`
- macOS VoiceOver, NSAccessibility, Voice Control in `accessibility-focus.md`
- macOS first responder debugging in `debugging.md`
- macOS layout patterns (sidebar, toolbar, multi-window, inspector, three-column) in `layout-patterns.md`
- macOS focus restoration (sheets, NSDocument revert) in `focus-restoration.md`

## [1.2.0] - 2026-04-08

### Added
- **Expanded iOS focus coverage** — game controller focus, Stage Manager multi-window, `.onKeyPress`, pointer hover effects, `focusedValue` / `focusedSceneValue` deep dive
- **Expanded watchOS focus coverage** — `.digitalCrownAccessory()`, nested scrolling conflicts, managing multiple focusable controls
- FAQ section with 20 collapsible questions (tvOS, iOS, iPadOS, watchOS, visionOS, macOS)
- `llms.txt` for AI model discovery
- SKILL.md keyword metadata for registry indexing
- Community health files: CONTRIBUTING.md, issue templates, PR template

## [1.1.0] - 2026-04-07

### Added
- **watchOS focus reference** — Digital Crown routing, sequential focus, `.focusable()` ordering, Crown conflicts
- **RealityKit focus reference** — `HoverEffectComponent`, collision shapes, shader effects, mixed SwiftUI + RealityKit hierarchies
- **Accessibility focus reference** — `@AccessibilityFocusState`, VoiceOver coordination, Full Keyboard Access, Switch Control, Reduce Motion

## [1.0.0] - 2026-04-06

### Added
- Initial release with 10 reference files covering tvOS, iOS/iPadOS, and visionOS
- SwiftUI and UIKit focus management
- 14 critical anti-patterns
- Focus styling, restoration, layout patterns, async coordination, debugging
- Agent Skills format (SKILL.md) for Claude Code, Codex, Cursor, Copilot, Gemini CLI
