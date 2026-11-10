surfaces: ios, web
agent: Claude
task: the UIKit arrangement reference app as an Exact screen

### Rough

- A pane scroller sized `height="100%"` came out 116 points taller than UIKit's allocation, and its last rows never scrolled into view. The kernel laid a scroller's platform insets (82 under the bar, 34 over the home indicator) out as padding, which a `content-box` height adds to. Fixed in exact2: the insets now come out of the content box, so the box keeps the size its rows give it.
- Without a height, a pane in native layout took its content's height (11,216 points) and did not scroll. A pane has to fill its allocation to scroll in it.
- With the safe-area adjustment disabled and no explicit content scroll view, a slow drag in the primary pane leaves the bar expanded; the UIKit reference minimizes it. An explicit primary selection minimizes it in both. Queued.
- The secondary pane's rows run under the open Duo's side strip, where the reference ends them at the pane's safe area. Arranged panes get no side clearance, and Contract cannot read a pane's own safe area yet. Queued.
- Hatching a pane scroller makes the development log attribute the scroll view's own gesture recognizers to the hatch.

### Lean in

- The route hatch sets UIKit's own `largeTitleDisplayMode`, `navigationBarMinimization` and `setContentScrollView(_:for:)` from `data-*` words; it runs after Exact projects the header, so they hold. Real drags match the reference: the primary pane minimizes the shared bar and the untouched pane moves up 58 points; Never, a secondary selection and fold poses behave as UIKit does.
- The header's buttons and `role="menu"` popovers become UIKit's bar items and nested `UIMenu`s with checkmarks, with no native menu code.
- A `state` initialized from a prop gives each pushed screen its own settings, seeded from the screen that pushed it.

### Needed

- Navigation item options for arranged panes: provided through the route hatch
- The pane scroller for an explicit bar selection: by hand, an `arrangement-pane` element hatch
- A pane's own side safe area: missing

### Checkpoints

- iOS 27.1 Duo simulator, open: Split Automatic, Vertical (UIKit shows the primary pane, as in the reference) and Overlay; Title Display Large; Next seeds the settings and Back keeps each screen's own; each pane scrolls to its last row.
- Real-touch drags: Automatic minimizes from the primary pane only; Never keeps the bar; Content Scroll View Secondary moves minimization to the secondary pane; Disabled with an explicit primary selection minimizes without moving the other pane.
- Folded at 130° and closed: UIKit's arrangement and Next work; at depth 2 UIKit moves the trailing items into an overflow menu beside Back and the tab bar.
- Web: the panes sit side by side at 951 points and stack at 420, with only Next; Next and Back work. The tab is last, so the closed Duo's compact tab bar keeps the other tabs where they were.
- `bun scripts/smoke.mjs duo --only arrangement,lab`: the Panes leg passes; the lab leg fails tapping Reflow after reopening the Duo, as it does on main.
