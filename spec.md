# Beam Designer

## Purpose
A beam design tool for engineers and students. The user sets up a beam,
places loads on it, and picks a steel or lumber section. The app shows the
shear, moment, and deflection diagrams and checks whether the beam is
strong and stiff enough, updating instantly as anything changes.

## Assumptions and limitations
- This tool is for educational and preliminary sizing use; it does not
  replace review by a qualified engineer or a complete code-compliant
  design.
- The listed material properties and allowable stresses are the fixed
  design inputs for this app. The app does not select or verify a building
  code, grade, or project-specific design criteria.
- Results apply only to the support types, load types, materials, and
  checks listed in this specification.

## Users
A civil engineering student checking a homework beam, or an engineer
sizing a floor beam or deck joist.

## Inputs
- Support type: Simply supported or Cantilever (fixed at the left end).
- Span: 4 to 40 ft, set with a slider and a number box that stay in sync.
- Span values outside this range are rejected with an input message; they
  are not silently changed.
- Loads, listed in a panel where each can be edited or removed:
  - Point loads: magnitude (kips, downward positive) and position (ft).
    A new point load starts at midspan (at the free end of a cantilever).
  - Uniform loads: magnitude (kips/ft) and start and end positions (ft).
  - Load magnitudes are nonnegative; upward loads are not supported.
    Point-load positions must be within the beam. Uniform-load start must
    be less than end; clip the loaded interval to the beam span. A uniform
    load with no overlap with the beam is invalid.
  - "Add point load" and "Add uniform load" buttons.
  - Point loads can also be dragged along the beam in the drawing,
    snapping to 0.25 ft.
  - If the span is shortened so a point load is outside the beam, flag
    that load and require it to be repositioned or removed before showing
    updated results. Uniform loads continue to be clipped to the beam.
- A zero-load case is valid and produces zero reactions, diagrams, stress,
  and deflection.
- Invalid values are identified beside the affected input. Do not silently
  replace them with defaults or show results based on invalid inputs.
- Material and section, from these tables:

  Steel W-shapes (E = 29,000 ksi, allowable bending stress Fb = 30 ksi):

  | Section | Ix (in^4) | Sx (in^3) | Weight (lb/ft) |
  |---------|-----------|-----------|----------------|
  | W8x10   | 30.8      | 7.81      | 10 |
  | W10x12  | 53.8      | 10.9      | 12 |
  | W12x26  | 204       | 33.4      | 26 |
  | W14x22  | 199       | 29.0      | 22 |
  | W16x26  | 301       | 38.4      | 26 |
  | W18x35  | 510       | 57.6      | 35 |
  | W21x44  | 843       | 81.6      | 44 |
  | W24x55  | 1350      | 114       | 55 |

  Douglas Fir-Larch No. 2 lumber (E = 1,600 ksi, Fb = 0.900 ksi):

  | Section | Ix (in^4) | Sx (in^3) |
  |---------|-----------|-----------|
  | 2x8     | 47.63     | 13.14     |
  | 2x10    | 98.93     | 21.39     |
  | 2x12    | 177.98    | 31.64     |

- Deflection limit: L/240 or L/360 (default L/360).
- Self-weight is ignored.

## Calculations
- Use x measured from the left end and L for the span (both in feet).
  Reactions satisfy equilibrium; a uniform load q over [a, b] has
  resultant q(b - a) at (a + b)/2.
- Sign convention: downward loads and deflection are positive, upward
  reactions are positive, positive shear acts upward on the left face, and
  positive moment is sagging. With downward q positive,
  dV/dx = -q and dM/dx = V; a downward point load P causes a shear jump
  of -P.
- Evaluate V (kips) and M (kip-ft) at 1 ft spacing or finer and at every
  load point and uniform-load endpoint. Include both one-sided shear
  values at point loads and find extrema from the response, not just
  display samples.
- For downward-positive deflection, d^2y/dx_in^2 = -M_in/(E Ix), where
  x_in = 12x, L_in = 12L, and M_in = 12M (M in kip-ft; M_in in kip-in).
  Use E in ksi and Ix in in^4. Boundary conditions: y = 0 at both simple
  supports; y = 0 and slope = 0 at the cantilever's fixed end.
- Bending stress is fb = (max |M| * 12 in/ft) / Sx (ksi), with M in
  kip-ft and Sx in in^3. It passes if fb <= Fb.
- Deflection passes if max |y| <= delta_allow, where
  delta_allow = L_in/240 or L_in/360. Check-card ratios are fb/Fb and
  max |y|/delta_allow.
- Evaluate both checks for each section in the selected material. Suggest
  the lightest passing steel section (ties go to larger Ix) or the first
  passing lumber size in the table.
- Use full precision for checks; each validation result must agree within
  half a unit of its last displayed decimal place.

## Outputs
- A beam drawing with the supports, load arrows (point loads as single
  arrows, uniform loads as a row of small arrows), and the reactions.
- Three diagrams stacked under the beam and lined up with it on the same
  x scale: shear (kips), moment (kip-ft), and deflected shape (in). Each
  diagram is filled and shows its maximum value labeled at its location.
- Moving the mouse over any diagram shows a vertical line across all three
  with the values of V, M, and y at that x.
- Two check cards: Bending and Deflection. Each shows the actual value, the
  allowable value, a bar for the ratio (green under 70%, amber 70-100%,
  red over 100%), and PASS or FAIL.
- A line suggesting the lightest passing section, with a button to use it.
- If no section in the selected material passes both checks, say that no
  listed section passes and do not show a use-section button.
- When inputs are invalid or a calculation cannot be completed, identify
  the problem and mark affected results unavailable; do not present stale
  or success-shaped results.
- Everything updates immediately whenever any input changes.

## Look and feel
- Opens with an example already loaded (20 ft simply supported W12x26
  with a 1 kip/ft uniform load over the full span), so it is useful the
  moment it opens.
- Clean and professional, like a commercial engineering web app: light
  background, white cards with soft shadows, one accent color, a modern
  system font, generous spacing. No clutter.
- Inputs in a panel on the left; drawing, diagrams, and checks on the
  right. On a phone, the panels stack.
- Every input has a short label with units. Hovering over a check card
  explains the formula used.
- Point-load dragging has a keyboard-accessible alternative through its
  position input. Explanations and diagram values are available without
  hover, including by keyboard focus and touch.
- Do not use color alone to communicate check status. Diagrams and labels
  remain readable and are not clipped on small screens.

## Validation
1. Simply supported, 20 ft, W12x26, uniform 1 kip/ft over the full span:
   reactions 10 k and 10 k; max shear 10 k; max moment 50.0 kip-ft at
   10 ft; fb = 17.96 ksi; max deflection 0.609 in at 10 ft; L/360 =
   0.667 in; both checks pass.
2. Simply supported, 20 ft, W12x26, point load 10 k at 5 ft: reactions
   7.5 k (left) and 2.5 k (right); max moment 37.5 kip-ft at 5 ft;
   fb = 13.47 ksi; max deflection 0.340 in at 8.82 ft.
3. Cantilever, 10 ft, W12x26, point load 5 k at the free end: reaction
   5 k and fixed-end moment 50 kip-ft; max deflection 0.487 in at the free
   end (PL^3/3EI); L/360 = 0.333 in, so deflection fails.
4. Simply supported 12 ft 2x10 joist, uniform 0.05 kip/ft: max moment
   0.90 kip-ft; fb = 0.505 ksi; max deflection 0.147 in; both pass.
5. Verify a zero-load case, loads at beam boundaries, uniform loads partly
   beyond the beam, shortening the span past a point load, invalid inputs,
   and a case where no catalog section passes.
6. Verify that point-load dragging and check explanations have keyboard-
   and touch-accessible alternatives, and that status remains clear without
   color alone.

## Out of scope
- Self-weight, shear stress, lateral-torsional buckling, load
  combinations, and continuous beams.
- Saving designs.