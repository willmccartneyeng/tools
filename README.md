# Web calculators — Will McCartney, PE

Two self-contained HTML files. No libraries, no build step, no external requests,
no data leaves the browser. Open either file directly in a browser to test.

- `arch-thrust-line.html` — thrust-line limit analysis of a masonry arch by graphic statics
- `stone-lintel-sizer.html` — allowable-stress check of a plain stone lintel

---

## Publishing

These pages are hosted as standalone files, not embedded in Carrd.

**Why not embedded.** Carrd inserts embed code into the published page as markup after
load. A `<script>` element added that way is parsed into the DOM but never executed — normal
browser behaviour, not a Carrd defect. Confirmed on the live site: the markup rendered, the
script element was present (`document.scripts` count of 1), and `data-booted` stayed `null`
with no console error, which rules out a Content Security Policy. Inline placement, Hidden
Body End placement, and a self-mounting loader using an `onload` attribute were all tried;
Carrd strips inline event-handler attributes as well.

**GitHub Pages.** Upload both HTML files to a public repository, then Settings → Pages →
Deploy from a branch → main → / (root). URLs follow the pattern:

    https://USERNAME.github.io/REPO/arch-thrust-line.html
    https://USERNAME.github.io/REPO/stone-lintel-sizer.html

Edit later through the pencil icon on the file view; the live page updates in about a
minute. Hard-reload with Ctrl+Shift+R, as the CDN caches.

**Linking from Carrd.** Add ordinary text links under Other Projects. Each tool opens as its
own page carrying the site palette and type, so it reads as part of the site, and each has a
URL that can be sent to a client or cited in a report. An `<iframe>` in an Inline Code embed
also works, since it is plain markup with nothing executable in it.

## Palette and type

Set at the top of each file:

    --tan:#D8D6CF        page ground
    --navy:#465172       text and rules
    --serif:"Libre Baskerville"    prose, headings, verdict line
    --sans:"Libre Franklin"        controls, tables, drawing labels

Fonts load from Google Fonts. The one colour outside this palette is `--tc-accent`
(`#9b2d20`), declared inside the calculator block and used for thrust lines, load triangles,
and moment diagrams — the traditional red of thrust-line plates.

## Arch thrust line — method

1. The ring is cut into radial joints at equal intrados arc length.
2. Each voussoir polygon and the spandrel fill above it are reduced to a vertical point
   load at the polygon centroid. Statics use a finer subdivision (240 minimum) than the
   voussoir count shown in the force polygon.
3. An analog simply supported beam spanning springing to springing gives M(x). Every
   possible line of thrust is `y(x) = c1 + c2*x + M(x)/H`.
4. For a trial H, admissibility at each joint is two linear constraints in (c1, c2):
   the extrados point above the line, the intrados point below it. Feasibility of that
   two-variable LP is checked by a refining sweep on c2.
5. Bisection on H gives Hmin and Hmax — the safe theorem, Heyman (1966, 1982).
6. Geometric factor of safety is Heyman's thickness ratio: the corridor is shrunk about
   the centerline, loads held constant, until only one line fits.

### Validation

Semicircular arch under self weight, radial cuts:

| quantity | tool | published |
|---|---|---|
| least thickness, t/R on centerline radius | 0.110 | 0.1075 radial cuts (Milankovitch); 0.1095 vertical cuts |
| rupture angle from crown | 54–55° | 54.5° |
| collapse mechanism | 5 hinges: extrados crown, intrados haunches, extrados springings | same |

Sources: J. Heyman, "The safety of masonry arches," *Int. J. Mech. Sci.* 11 (1969) 363–385;
J. Heyman, *The Masonry Arch*, Ellis Horwood, 1982; N. Makris and H. Alexakis, "The effect
of stereotomy on the shape of the thrust-line and the minimum thickness of semicircular
masonry arches," *Arch. Appl. Mech.* 83 (2013) 1511–1533.

The tool runs about 2% conservative against Milankovitch. A ring below its limiting
thickness returns no admissible line rather than a spurious result.

### Assumptions

Zero tensile strength, infinite compressive strength, no sliding at the joints. Compressive
stress at the joints is **not** checked. Abutment movement, arch–fill interaction beyond
dead weight, and out-of-plane behavior are not modeled.

---

## Stone lintel sizer — method

Distributed load is built as an intensity function over the effective span, so three cases
fall out of one integration:

- wall deeper than the arching apex → triangle, apex at `(Lc/2)·tan(theta)` above the lintel
- wall shallower than the apex → truncated trapezoid, `h(x) = min(hw, tan(theta)·min(u, Lc-u))`
- arching off → full rectangle of wall

Effective span defaults to clear span plus member depth, capped at center-to-center of
bearing — the rule TMS 402 gives for masonry beams. Two alternatives are selectable.

Checks:

| check | expression |
|---|---|
| bending | `fb = M/S`, `S = b·d²/6`, against `Fb = fr/FS` |
| shear | `fv = 1.5·V/(b·d)` |
| bearing | `fp = R/(b·lb)` |
| deflection | double integration of M/EI, ends pinned |

Hand check, 48 in opening, 8 × 8 in lintel, 8 in bearing, 45° arching, 120 pcf wall,
150 pcf stone: triangle 320 lb, self weight 66.7 plf, M = 5,378 lb·in, fb = 63.0 psi,
72% of an 87.5 psi allowable. Tool agrees.

### Inputs that must be replaced before real use

Modulus of rupture defaults to 700 psi and the flexural factor of safety to 8. Both are
placeholders. Use ASTM C99 or ASTM C880 test data for the specific stone and quarry bed
orientation. Allowable shear (100 psi) and bearing (250 psi) defaults are likewise generic.

---

## Draft disclaimer for the page

> These calculators are published for preliminary sizing and instruction. They are not a
> substitute for engineering judgment, project-specific analysis, or a sealed design, and no
> engineer–client relationship arises from their use. Results depend entirely on inputs
> supplied by the user, including material strengths that must come from test data for the
> stone or masonry actually used. Neither tool checks compressive stress at joints; the arch
> tool assumes zero tensile strength, rigid blocks, and no sliding. Verify all output
> independently before relying on it.
