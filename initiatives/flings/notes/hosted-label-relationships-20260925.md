# Hosted label relationships — September 25, 2026 (UTC)

## Environment and scope

Checked the existing [Flings test Site](https://flings-test.ken-novak.chatgpt.site)
with the native owner's organizer session in the Codex in-app browser. The Sites
read API reported version 14 and public access-policy revision 2. The viewport
was 1280 × 720 CSS pixels, with device pixel ratio 2. Browser and engine version
were not exposed by the available read-only inspection surface.

The existing fictional gathering was
`a83949b1-befc-4581-bb95-1a5b0d125ad7`. All editors were cancelled without saving;
the deletion dialog was closed with **Keep gathering**, with the title empty,
acknowledgment unchecked and permanent-delete action disabled. Member pages
were the organizer's read-only previews. No business records, messages, files,
credentials, deployments or audience settings changed.

## Eleven completed DOM observations

The probe checked document IDs for duplicates, seven ARIA ID-reference
attributes for missing or ambiguous targets, explicit `label[for]` targets for
uniqueness, and rendered native controls for a label/name candidate.

Every row had zero duplicate IDs, invalid ARIA targets, invalid explicit label
targets and native controls without a label/name candidate.

| Loaded state | IDs | ARIA reference tokens | Explicit label targets | Native controls with rectangles |
|---|---:|---:|---:|---:|
| Organizer, editors/history collapsed | 20 | 3 | 10 | 13 |
| Write a post | 22 | 3 | 12 | 15 |
| Create a poll, including eligible-member checkbox | 27 | 4 | 15 | 19 |
| Request a payment | 26 | 3 | 16 | 19 |
| Record a ledger adjustment | 24 | 3 | 14 | 17 |
| Edit fling details | 23 | 3 | 13 | 16 |
| Fling details and Alex's profile open together | 27 | 3 | 17 | 20 |
| Initial deletion dialog | 28 | 6 | 12 | 15 |
| Message review history expanded | 20 | 3 | 10 | 13 |
| Accepted Alex Morgan preview | 20 | 6 | 4 | 8 |
| Invited Robin Reed preview | 8 | 2 | 4 | 4 |
| **Total observations** | **245** | **39** | **127** | **159** |

Totals include repeated elements across states; they are not counts of unique
controls. The simultaneous-editor observation checks that their field IDs do
not collide. The dialog adds its heading/description and acknowledgment
references. The accepted preview includes disabled poll choices; the invited
preview contains only its disabled contact controls among native inputs.

## Reproduce the read-only probe

Open the populated fictional gathering with an assigned organizer. Observe the
collapsed page, open each listed editor separately and cancel it, open the fling
and profile editors together and cancel both, open/cancel the deletion dialog,
expand message history, and visit each member preview. After the corresponding
content has loaded, evaluate this function against that document:

```js
() => {
  const ids = Array.from(document.querySelectorAll('[id]')).map(e => e.id);
  const duplicates = ids.filter((id, i) => ids.indexOf(id) !== i);
  const attrs = [
    'aria-labelledby', 'aria-describedby', 'aria-controls', 'aria-owns',
    'aria-details', 'aria-errormessage', 'aria-activedescendant',
  ];
  const refs = Array.from(document.querySelectorAll(
    attrs.map(a => '[' + a + ']').join(','),
  )).flatMap(e => attrs.flatMap(a => (e.getAttribute(a) || '')
    .split(/\s+/).filter(Boolean).map(id => ({
      attr: a, target: id, count: ids.filter(v => v === id).length,
    }))));
  const labels = Array.from(document.querySelectorAll('label[for]'))
    .map(e => ({
      target: e.htmlFor, count: ids.filter(id => id === e.htmlFor).length,
    }));
  const controls = Array.from(document.querySelectorAll(
    'input:not([type=hidden]),select,textarea',
  )).filter(e => e.getClientRects().length);
  return {
    url: location.pathname,
    ids: ids.length,
    idDuplicates: duplicates,
    ariaTokens: refs.length,
    invalidAria: refs.filter(r => r.count !== 1),
    labelFors: labels.length,
    invalidLabelFors: labels.filter(r => r.count !== 1),
    visibleControls: controls.length,
    unnamedNativeControls: controls.filter(e =>
      !e.labels?.length && !e.getAttribute('aria-label')?.trim() &&
      !e.getAttribute('aria-labelledby')?.trim() &&
      !e.getAttribute('title')?.trim(),
    ).map(e => ({ tag: e.tagName, id: e.id, type: e.type })),
  };
}
```

## Limits and interrupted attempts

This is a structural DOM check, not axe, a full accessible-name computation or
a screen-reader walkthrough. A label/name candidate can still be empty,
misleading or inappropriate; unique references do not prove suitable semantics.
The probe does not validate every ARIA attribute or custom-widget role. Controls
with rectangles may be outside the viewport, covered, inert or hidden from the
accessibility tree, including content behind a dialog. Their count is not a
visibility, focusability or accessibility conformance result.

Browser zoom shortcuts left the measured 1280 × 720 viewport, device pixel ratio
and 44-pixel heading size unchanged. They are not zoom evidence; the reset
shortcut was issued before the recorded gathering observations. A broad initial
DOM probe timed out and was replaced by the narrower selector-based probe above.
Some browser input calls timed out; subsequent page observations established
whether the requested editor actually opened before any result was recorded.
Transient loading states and failed attempts are excluded. The temporary tab
was closed after verification.

This adds targeted T12 evidence at one desktop size. It does not establish
text-zoom, phone layout, submission/error states, independent organizer access,
the full hosted browser matrix, screen-reader acceptance, hosted concurrent
writes or managed-provider backup/restart behavior. `verify-hosted-test` remains
actionable and Phase 6 remains incomplete.
