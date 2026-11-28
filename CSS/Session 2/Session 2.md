# CSS Positioning & Visual Effects: A Practical Reference

A progressive guide to eight core CSS topics. Each section starts with a short explanation, followed by realistic examples with comments explaining **why** each line exists.

## Table of Contents

1. [Positions](#1-positions)
2. [z-index](#2-z-index)
3. [Pseudo-elements (`::before`, `::after`)](#3-pseudo-elements-before-after)
4. [Pseudo-classes](#4-pseudo-classes)
5. [Transitions](#5-transitions)
6. [Opacity](#6-opacity)
7. [Overflow](#7-overflow)
8. [Shadows (text and box)](#8-shadows-text--box)

---

## 1. Positions

The `position` property controls **how an element is placed on the page** and whether the offset properties (`top`, `right`, `bottom`, `left`, `inset`) have any effect.

| Value      | In normal flow? | Offsets work? | Positioned relative to…                       | Typical use                           |
| ---------- | --------------- | ------------- | --------------------------------------------- | ------------------------------------- |
| `static`   | Yes             | No            | N/A (default)                                 | Normal content                        |
| `relative` | Yes             | Yes           | Its own original spot                         | Nudging, or an anchor for children    |
| `absolute` | No              | Yes           | Nearest **positioned** ancestor (or the page) | Badges, tooltips, overlays            |
| `sticky`   | Yes             | Yes           | Nearest scrolling ancestor, after a threshold | Sticky headers, table headings        |
| `fixed`    | No              | Yes           | The **viewport**                              | Chat buttons, cookie banners, navbars |

Sample HTML used for the first few examples:

```html
<div class="box">Box</div>
```

### 1.1 `static` (the default)

Every element is `static` unless you change it. It follows the normal document flow, and `top`/`left`/etc. are **ignored**.

```css
.box {
  position: static; /* default: you rarely write this, except to RESET a position set elsewhere */
  width: 100px;
  height: 100px;
  background-color: red;
  top: 50px; /* IGNORED: offsets do nothing on static elements */
  left: 50px; /* IGNORED */
}
```

### 1.2 `relative`

The element stays in the flow (its original space is **kept reserved**), but you can shift it visually. Its two main jobs:

1. Small visual nudges.
2. Becoming the **anchor** (containing block) for `absolute` children.

```html
<button class="notify-btn">
  Inbox
  <span class="dot"></span>
</button>
```

```css
.notify-btn {
  position: relative; /* WHY: makes this button the reference point for .dot below.
                         Without it, the dot would position itself against the whole page. */
  padding: 10px 18px;
}

.dot {
  position: absolute; /* taken out of flow, so it doesn't push the text */
  top: -4px; /* measured from the button's top edge, thanks to the relative parent */
  right: -4px;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: crimson;
}
```

Nudging example (the original space stays reserved, so neighbours do **not** move):

```css
.icon {
  position: relative;
  top: 2px; /* WHY: optically aligns an icon with text baseline without changing layout */
}
```

### 1.3 `absolute`

The element is **removed from the flow** (siblings behave as if it doesn't exist) and is placed relative to the **nearest ancestor whose position is not `static`**. If none exists, it uses the initial containing block (roughly the page).

```html
<div class="card">
  <span class="badge">SALE</span>
  <h3>Wireless Headphones</h3>
</div>
```

```css
.card {
  position: relative; /* anchor for the badge */
  padding: 24px;
  border: 1px solid #ddd;
}

.badge {
  position: absolute;
  top: 12px; /* 12px down from the card's top edge */
  right: 12px; /* 12px in from the card's right edge */
  background: #e63946;
  color: #fff;
  padding: 2px 8px;
  font-size: 12px;
}
```

**Centering trick** (very common for overlays and modals):

```css
.modal {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  /* WHY: top/left:50% place the element's TOP-LEFT corner at the center.
     translate(-50%, -50%) shifts it back by half its own size, so its CENTER lands there. */
}
```

**Stretching to fill the parent** with `inset`:

```css
.overlay {
  position: absolute;
  inset: 0; /* shorthand for top:0; right:0; bottom:0; left:0 → covers the whole positioned parent */
}
```

> **Common pitfall:** forgetting `position: relative` on the parent. The child then escapes and positions itself against the page.

### 1.4 `sticky`

A hybrid: the element behaves like `relative` until you scroll past a threshold, then it "sticks" like `fixed`, **but only within its parent's boundaries**.

```css
.table-header th {
  position: sticky;
  top: 0; /* REQUIRED: tells the browser WHEN to stick (0px from the top of the scroll container) */
  background: #fff; /* WHY: without a background, rows scroll visibly through the header */
}
```

Sticky section titles (like a contacts list):

```html
<section>
  <h2 class="letter">A</h2>
  <p>Aisha</p>
  <p>Ahmed</p>
  <p>Amir</p>
</section>
<section>
  <h2 class="letter">B</h2>
  <p>Bassem</p>
  <p>Basma</p>
</section>
```

```css
.letter {
  position: sticky;
  top: 0;
  background: #f1f1f1;
  /* WHY it swaps nicely: each <h2> can only stick inside its own <section>,
     so "B" pushes "A" out as you scroll into the next section. */
}
```

> **Why sticky "doesn't work":** the usual causes are (1) no `top`/`bottom`/`left`/`right` value, (2) an ancestor with `overflow: hidden/auto/scroll` that becomes the scroll container, (3) a parent that is no taller than the sticky element.

### 1.5 `fixed`

Removed from the flow and pinned relative to the **viewport**. It does not move when the page scrolls.

```html
<a href="#top" class="back-to-top">↑ Top</a>
```

```css
.back-to-top {
  position: fixed;
  bottom: 24px; /* always 24px from the bottom of the screen */
  right: 24px;
  padding: 12px 16px;
  background: #222;
  color: #fff;
  border-radius: 999px;
  z-index: 100; /* WHY: keeps it above page content that might overlap it (see section 2) */
}
```

Fixed navbar (and why you need to compensate for it):

```css
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%; /* fixed elements don't stretch automatically like block elements */
  height: 64px;
  background: #fff;
}

body {
  padding-top: 64px; /* WHY: a fixed element leaves the flow, so content would slide UNDER the navbar.
                          This padding reserves the space. */
}
```

> **Gotcha:** a `fixed` element becomes relative to an ancestor instead of the viewport if that ancestor has `transform`, `filter`, or `perspective` set.

### Quick comparison: `absolute` vs `fixed` vs `sticky`

| Question                          | absolute |     fixed     |        sticky         |
| --------------------------------- | :------: | :-----------: | :-------------------: |
| Leaves its space in the flow?     |    No    |      No       |          Yes          |
| Scrolls with the page?            |   Yes    |      No       |  Until the threshold  |
| Depends on a positioned ancestor? |   Yes    | No (viewport) | No (scroll container) |

---

## 2. z-index

`z-index` controls the **stacking order** (which element appears on top when elements overlap). Higher numbers sit in front.

**Rules you must know:**

1. It only works on **positioned** elements (`relative`, `absolute`, `fixed`, `sticky`), **or** on flex/grid items.
2. It can be negative, which pushes the element behind its parent's content.
3. Elements create **stacking contexts**, which are isolated "layers". A child can never escape its parent's context, no matter how big its z-index is.

### 2.1 Basic layering

```html
<div class="stack">
  <div class="layer red">Red</div>
  <div class="layer blue">Blue</div>
</div>
```

```css
.stack {
  position: relative;
  height: 150px;
}

.layer {
  position: absolute; /* WHY: z-index needs a positioned element to take effect */
  width: 120px;
  height: 120px;
}

.red {
  top: 0;
  left: 0;
  background: tomato;
  z-index: 2;
}
.blue {
  top: 40px;
  left: 40px;
  background: royalblue;
  z-index: 1;
}
/* Red has the higher z-index, so it overlaps blue even though it comes first in the HTML. */
```

Without `z-index`, later elements in the HTML paint on top of earlier ones.

### 2.2 A sensible z-index scale

Magic numbers like `99999` become unmaintainable. Define a scale with custom properties:

```css
:root {
  --z-base: 1;
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-modal-backdrop: 300;
  --z-modal: 310;
  --z-toast: 400;
  /* WHY: one place to see and change layer order; leaving gaps (100, 200…) lets you insert layers later. */
}

.dropdown {
  position: absolute;
  z-index: var(--z-dropdown);
}
.site-header {
  position: sticky;
  top: 0;
  z-index: var(--z-sticky);
}
.modal-backdrop {
  position: fixed;
  inset: 0;
  z-index: var(--z-modal-backdrop);
  background: rgb(0 0 0 / 0.5);
}
.modal {
  position: fixed;
  z-index: var(--z-modal);
}
```

### 2.3 Stacking contexts: why `z-index: 9999` sometimes fails

A new stacking context is created by, among others: the root element, a positioned element with a `z-index` other than `auto`, `opacity < 1`, `transform`, `filter`, and `isolation: isolate`.

```html
<div class="parent-a"><div class="child-a">Child A</div></div>
<div class="parent-b">Parent B</div>
```

```css
.parent-a {
  position: relative;
  z-index: 1;
} /* creates a context at level 1 */
.child-a {
  position: absolute;
  z-index: 9999;
} /* huge number, but only wins INSIDE parent-a */

.parent-b {
  position: relative;
  z-index: 2;
} /* level 2 beats the entire parent-a context */
/* Result: parent-b covers child-a, because child-a is trapped inside parent-a's level-1 layer. */
```

**Fix with `isolation`:** put `isolation: isolate` on a component to create a contained context on purpose, so its internal z-indexes never leak out.

```css
.card {
  isolation: isolate; /* WHY: the card's internal layers (badge, overlay) stay self-contained
                         and can't accidentally cover the site header. */
}
```

### 2.4 Negative z-index for decorative backgrounds

```css
.hero {
  position: relative;
  isolation: isolate; /* keeps the -1 layer INSIDE the hero instead of dropping behind the page background */
}

.hero::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, #4facfe, #00f2fe);
  z-index: -1; /* sits behind the hero's text but above the hero's own background */
}
```

---

## 3. Pseudo-elements (`::before`, `::after`)

Pseudo-elements let you **insert decorative content via CSS** without extra HTML. `::before` is inserted as the element's **first child**, and `::after` as its **last child**.

**Key facts:**

- They **only render if `content` is set** (even an empty string `""`).
- They are `inline` by default. Add `display: block` or use positioning to size them.
- Use double colons (`::before`) in modern CSS. Single-colon `:before` is the legacy syntax.
- They can't be selected by JavaScript directly and aren't reliably read by screen readers. **Don't put essential information in them.**

### 3.1 Inserting text/icons

```css
/* Mark external links so users know they leave the site */
a[href^="http"]::after {
  content: " ↗"; /* the content property is what makes the pseudo-element exist */
  font-size: 0.8em;
}

/* Required-field indicator without touching the HTML */
label.required::after {
  content: " *";
  color: crimson;
}
```

### 3.2 Reading from attributes with `attr()`

```html
<abbr data-tip="HyperText Markup Language">HTML</abbr>
```

```css
abbr {
  position: relative; /* anchor for the tooltip */
  cursor: help;
  text-decoration: underline dotted;
}

abbr::after {
  content: attr(
    data-tip
  ); /* WHY: pulls the text from the HTML attribute, so each tooltip stays in the markup */
  position: absolute;
  bottom: 125%; /* sits above the word */
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap; /* stops the tooltip wrapping to one word per line */
  background: #222;
  color: #fff;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  opacity: 0; /* hidden until hover (see transitions and opacity below) */
  pointer-events: none; /* so the invisible tooltip doesn't block clicks */
  transition: opacity 0.2s ease;
}

abbr:hover::after {
  opacity: 1;
}
```

### 3.3 Decorative shapes (no extra markup)

**Animated underline for links:**

```css
.nav-link {
  position: relative;
  text-decoration: none;
}

.nav-link::after {
  content: ""; /* empty string: we only want a shape, not text */
  position: absolute;
  left: 0;
  bottom: -4px;
  width: 100%;
  height: 2px;
  background: currentColor; /* inherits the link's text color automatically */
  transform: scaleX(0); /* collapsed to nothing */
  transform-origin: left; /* grows from the left edge */
  transition: transform 0.3s ease;
}

.nav-link:hover::after {
  transform: scaleX(1);
}
```

**Speech-bubble arrow (a CSS triangle):**

```css
.bubble {
  position: relative;
  background: #2d6cdf;
  color: #fff;
  padding: 12px 16px;
  border-radius: 8px;
}

.bubble::after {
  content: "";
  position: absolute;
  top: 100%; /* right below the bubble */
  left: 20px;
  border: 8px solid transparent; /* a zero-size box with thick borders... */
  border-top-color: #2d6cdf; /* ...where only ONE colored border shows, forming a triangle */
}
```

### 3.4 Overlay and clearfix patterns

```css
/* Dark gradient over an image so white text stays readable */
.thumb {
  position: relative;
}

.thumb::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgb(0 0 0 / 0.7), transparent);
  pointer-events: none; /* WHY: lets clicks pass through to the link/image underneath */
}
```

```css
/* Clearfix: makes a parent wrap its floated children */
.clearfix::after {
  content: "";
  display: table; /* must be block-level for clear to work */
  clear: both;
}
```

### 3.5 Quotation marks and counters

```css
blockquote::before {
  content: "“"; /* decorative quote mark */
  font-size: 3rem;
  line-height: 0;
  color: #bbb;
  vertical-align: -0.4em;
}

/* Auto-numbered headings */
body {
  counter-reset: section;
}
h2::before {
  counter-increment: section;
  content: counter(section) ". "; /* produces "1. ", "2. ", … without manual numbering */
}
```

---

## 4. Pseudo-classes

A pseudo-class selects an element **based on its state or position**, without adding classes in the HTML. It uses a **single colon** (`:hover`), while pseudo-elements (section 3) use a double colon (`::after`) because they create a _new_ piece of content.

|         | Pseudo-class                                       | Pseudo-element                            |
| ------- | -------------------------------------------------- | ----------------------------------------- |
| Syntax  | `:hover`                                           | `::after`                                 |
| Selects | An existing element in a certain state or position | A generated or partial part of an element |
| Example | `li:first-child`                                   | `p::first-line`                           |

They can be combined: `a:hover::after` means "the `::after` of a link **while hovered**" (used in sections 3 and 9).

### 4.1 User-action states: `:hover`, `:active`, `:focus`, `:focus-visible`, `:focus-within`

```html
<button class="btn">Save</button>
<input class="field" type="text" placeholder="Your email" />
<form class="signup">
  <input type="email" placeholder="Email" />
  <button>Join</button>
</form>
```

```css
.btn:hover {
  background: #1b4fb0; /* pointer is over the button: signals "this is clickable" */
}

.btn:active {
  transform: translateY(
    1px
  ); /* while pressed: tiny push gives tactile feedback */
}

.field:focus {
  border-color: #2d6cdf; /* element has keyboard/mouse focus: shows where typing will go */
}

.btn:focus-visible {
  outline: 3px solid #2d6cdf; /* WHY :focus-visible: shows the ring for KEYBOARD users only,
                                 so mouse clicks don't leave an ugly ring on buttons */
  outline-offset: 2px;
}

.signup:focus-within {
  box-shadow: 0 0 0 3px rgb(45 108 223 / 0.3); /* WHY: highlights the whole form
                                                  when ANY child input is focused */
}
```

> **Order matters for links (LVHA):** write `:link`, `:visited`, `:hover`, `:active` in that order. They have equal specificity, so the later rule wins when several apply.

```css
a:link {
  color: #2d6cdf;
} /* unvisited links */
a:visited {
  color: #7c3aed;
} /* links already opened */
a:hover {
  color: #1b4fb0;
}
a:active {
  color: #c1121f;
}
```

### 4.2 Form states: `:checked`, `:disabled`, `:required`, `:valid`, `:invalid`, `:placeholder-shown`

```html
<label class="switch">
  <input type="checkbox" />
  <span class="track"></span>
  Email notifications
</label>

<input type="email" required placeholder="you@example.com" />
<button disabled>Pay now</button>
```

```css
/* Custom toggle: style a sibling based on the checkbox state */
.switch input {
  position: absolute;
  opacity: 0;
} /* hide the native box but keep it accessible */

.track {
  display: inline-block;
  width: 40px;
  height: 22px;
  border-radius: 999px;
  background: #cbd5e1;
  transition: background-color 0.2s ease;
}

.switch input:checked + .track {
  background: #22c55e; /* WHY the + combinator: the sibling after the input reacts to its :checked state */
}

.switch input:focus-visible + .track {
  outline: 3px solid #2d6cdf; /* keep keyboard users oriented even though the input is hidden */
}

/* Validation feedback: only show errors AFTER the user typed something */
input:required {
  border-left: 4px solid #f59e0b;
} /* marks mandatory fields */
input:valid {
  border-color: #22c55e;
}
input:invalid:not(:placeholder-shown) {
  border-color: #e63946; /* WHY :not(:placeholder-shown): an empty field isn't "wrong" yet,
                             so we avoid showing red errors before the user starts typing */
}

/* Disabled controls */
button:disabled {
  opacity: 0.5; /* see section 6 */
  cursor: not-allowed;
}
```

### 4.3 Structural pseudo-classes: position in the family tree

| Selector                                               | Matches                                             |
| ------------------------------------------------------ | --------------------------------------------------- |
| `:first-child` / `:last-child`                         | First / last child of its parent                    |
| `:only-child`                                          | An element with no siblings                         |
| `:nth-child(n)`                                        | The n-th child (`2`, `odd`, `even`, `3n`, `n+4`)    |
| `:nth-last-child(n)`                                   | Counts from the end                                 |
| `:first-of-type` / `:last-of-type` / `:nth-of-type(n)` | Same idea, but counts only elements of the same tag |
| `:empty`                                               | Elements with no children and no text               |

```html
<ul class="menu">
  <li>Dashboard</li>
  <li>Orders</li>
  <li>Customers</li>
  <li>Reports</li>
  <li>Settings</li>
</ul>
```

```css
/* Separator lines between items, but NOT after the last one */
.menu li {
  border-bottom: 1px solid #e2e8f0;
}
.menu li:last-child {
  border-bottom: 0;
} /* WHY: avoids a double line against the container's own border */

/* Zebra striping for readability in tables and lists */
.menu li:nth-child(even) {
  background: #f8fafc;
}

/* Highlight every 3rd item, then only the first three items */
.menu li:nth-child(3n) {
  font-weight: 600;
}
.menu li:nth-child(-n + 3) {
  color: #1f6feb;
} /* "-n+3" = items 1, 2, 3 */

/* Hide empty containers so they don't leave a blank gap */
.note:empty {
  display: none;
}
```

**`nth-child` vs `nth-of-type`:** in a container with an `<h2>` followed by three `<p>`, `p:nth-child(1)` matches **nothing** (the first child is the `h2`), while `p:nth-of-type(1)` matches the first paragraph.

### 4.4 Logical pseudo-classes: `:not()`, `:is()`, `:where()`, `:has()`

```css
/* :not(): exclude something */
.card:not(.featured) {
  opacity: 0.9;
}
li:not(:last-child) {
  margin-bottom: 8px;
} /* spacing between items, none after the last */

/* :is(): group selectors to avoid repetition */
:is(h1, h2, h3):hover {
  color: #1f6feb;
}
/* WHY: replaces  h1:hover, h2:hover, h3:hover  with one rule */

/* :where(): same as :is(), but with ZERO specificity, so it is easy to override later */
:where(.prose) a {
  text-decoration: underline;
}

/* :has(): select a PARENT based on its children (the "parent selector") */
.card:has(img) {
  padding-top: 0;
} /* cards that contain an image lose top padding */
.field:has(input:invalid) label {
  color: #e63946;
} /* label turns red when its input is invalid */
form:has(input:checked) .submit {
  opacity: 1;
} /* enable look once something is selected */
```

### 4.5 Other useful pseudo-classes

```css
:root {
  --brand: #1f6feb;
} /* :root = the <html> element; the standard home for CSS variables */

a:target,
section:target {
  scroll-margin-top: 80px;
} /* :target = element whose id matches the URL #hash;
                                                 the margin keeps it from hiding under a sticky header (section 1.4) */

input:read-only {
  background: #f1f5f9;
}
input:autofill {
  box-shadow: inset 0 0 0 100px #e8f0ff;
} /* style browser-autofilled fields */

@media (hover: hover) {
  .card:hover {
    transform: translateY(-4px);
  }
  /* WHY: touch screens have no real hover, so we apply hover effects only where a pointer exists */
}
```

### 4.6 Specificity note

Each pseudo-class adds the same weight as a **class** (`0,1,0`), while pseudo-elements count like an element (`0,0,1`).

```css
a.link:hover {
} /* 0,2,1: class + pseudo-class + element */
a::after {
} /* 0,0,2: element + pseudo-element */
:where(a):hover {
} /* 0,1,0: :where() contributes nothing */
```

### 4.7 Mini example: accessible tooltip button

Combining pseudo-classes **and** pseudo-elements (section 3), plus transitions and opacity (sections 5 and 6):

```html
<button class="help" data-tip="Opens the billing settings">Billing</button>
```

```css
.help {
  position: relative; /* anchor for the tooltip (section 1.2) */
  padding: 8px 16px;
  border: 1px solid #cbd5e1;
  border-radius: 8px;
  background: #fff;
}

.help::after {
  content: attr(data-tip); /* pseudo-ELEMENT: generates the tooltip text */
  position: absolute;
  bottom: calc(100% + 8px);
  left: 50%;
  transform: translateX(-50%);
  white-space: nowrap;
  padding: 4px 8px;
  border-radius: 4px;
  background: #1c2530;
  color: #fff;
  font-size: 12px;
  opacity: 0; /* hidden by default */
  pointer-events: none;
  transition: opacity 0.2s ease;
}

.help:hover::after,
.help:focus-visible::after {
  opacity: 1; /* pseudo-CLASSES decide WHEN it shows: mouse hover OR keyboard focus */
}

.help:focus-visible {
  outline: 3px solid #2d6cdf;
  outline-offset: 2px;
}
.help:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

### Pseudo-classes vs pseudo-elements: quick recap

| Question                    | Pseudo-class                                         | Pseudo-element                                               |
| --------------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| Colons                      | One (`:`)                                            | Two (`::`)                                                   |
| Needs `content`?            | No                                                   | Yes (for `::before`/`::after`)                               |
| Typical question it answers | "What **state** or **position** is this element in?" | "What **extra part** of this element can I style or insert?" |
| Examples                    | `:hover`, `:checked`, `:nth-child()`, `:not()`       | `::before`, `::after`, `::first-line`, `::placeholder`       |

---

## 5. Transitions

A transition animates a property **smoothly between two states** (for example, normal → hover) instead of jumping instantly.

**Shorthand order:** `transition: <property> <duration> <timing-function> <delay>;`

| Property                     | What it does                     | Example                            |
| ---------------------------- | -------------------------------- | ---------------------------------- |
| `transition-property`        | Which CSS property to animate    | `background-color`, `all`          |
| `transition-duration`        | How long it takes                | `0.3s`, `250ms`                    |
| `transition-timing-function` | The speed curve                  | `ease`, `linear`, `cubic-bezier()` |
| `transition-delay`           | How long to wait before starting | `0s`, `0.2s`                       |

> Put the `transition` on the **base** state (not just `:hover`). That way it animates **both in and out**.

### 5.1 `transition-property` and the shorthand

```css
.btn {
  background: #2d6cdf;
  color: #fff;
  transition: background-color 0.25s ease; /* only animate the color; explicit is faster and safer than "all" */
}

.btn:hover {
  background: #1b4fb0;
}
```

Animating multiple properties with different settings:

```css
.card {
  transition:
    transform 0.3s ease-out,
    /* lift: quick and snappy */ box-shadow 0.5s ease-out; /* shadow: slightly slower, so it feels soft */
}

.card:hover {
  transform: translateY(-6px);
  box-shadow: 0 16px 32px rgb(0 0 0 / 0.18);
}
```

> **Performance tip:** prefer animating `transform` and `opacity`. They're cheap for the browser. Animating `width`, `height`, `top`, or `left` forces layout recalculation and can stutter.

> **Not animatable:** `display` and `height: auto` don't transition. Use `opacity`/`visibility`, `max-height`, or `grid-template-rows: 0fr → 1fr`.

### 5.2 `transition-duration`

```css
.icon-btn {
  transition: transform 150ms;
} /* micro-interactions: 100–200ms feel instant and responsive */
.dropdown {
  transition: opacity 300ms;
} /* small UI changes: 200–300ms */
.side-panel {
  transition: transform 500ms;
} /* large movements: 400–600ms so the eye can follow */
```

| Interaction type    | Suggested duration |
| ------------------- | ------------------ |
| Hover color changes | 150–250 ms         |
| Dropdowns, tooltips | 200–300 ms         |
| Modals, side panels | 300–500 ms         |

### 5.3 `transition-timing-function`

Controls **acceleration**: how the animation's speed changes over its duration.

| Value                       | Feel                           | Good for                          |
| --------------------------- | ------------------------------ | --------------------------------- |
| `ease`                      | Fast start, slow end (default) | General purpose                   |
| `linear`                    | Constant speed                 | Progress bars, spinners           |
| `ease-in`                   | Slow start, fast end           | Elements **leaving** the screen   |
| `ease-out`                  | Fast start, slow end           | Elements **entering** the screen  |
| `ease-in-out`               | Slow start and end             | Elements moving across the screen |
| `cubic-bezier(x1,y1,x2,y2)` | Custom curve                   | Bouncy or branded motion          |
| `steps(n)`                  | Jumps in discrete frames       | Sprite or typewriter effects      |

```css
/* Progress bar: constant speed matches "real" progress */
.progress-fill {
  width: 0;
  transition: width 2s linear;
}
.progress.loaded .progress-fill {
  width: 100%;
}
```

```css
/* Bouncy "overshoot" using cubic-bezier (y values > 1 go past the target, then settle) */
.like-btn {
  transition: transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}
.like-btn:hover {
  transform: scale(1.2);
}
```

```css
/* Typewriter-style reveal: steps() makes the width grow in 20 jumps (one per character) */
.typing {
  width: 0;
  overflow: hidden; /* hides the not-yet-revealed text (see section 7) */
  white-space: nowrap;
  transition: width 2s steps(20, end);
}
.typing.active {
  width: 20ch;
}
```

### 5.4 `transition-delay`

Waits before starting. Useful for **staggering** and for avoiding accidental triggers.

```css
/* Dropdown that closes with a short delay, so a slightly wandering mouse doesn't instantly dismiss it */
.menu {
  opacity: 0;
  visibility: hidden;
  transition:
    opacity 0.2s ease 0.3s,
    visibility 0s linear 0.5s; /* delay hiding by 300ms */
}

.nav-item:hover .menu {
  opacity: 1;
  visibility: visible;
  transition-delay: 0s; /* WHY: opening is instant; only CLOSING gets the grace period */
}
```

Staggered list items using a custom property:

```css
.list li {
  opacity: 0;
  transform: translateY(12px);
  transition:
    opacity 0.4s ease,
    transform 0.4s ease;
  transition-delay: calc(
    var(--i) * 80ms
  ); /* each item starts 80ms after the previous one */
}

.list.show li {
  opacity: 1;
  transform: none;
}
```

```html
<ul class="list">
  <li style="--i:0">Dashboard</li>
  <li style="--i:1">Orders</li>
  <li style="--i:2">Customers</li>
</ul>
```

### 5.5 Respect reduced-motion preferences

```css
@media (prefers-reduced-motion: reduce) {
  * {
    transition-duration: 0.01ms !important; /* WHY: users with vestibular disorders opt out of motion */
  }
}
```

---

## 6. Opacity

`opacity` sets the **transparency of an element and everything inside it**, from `0` (invisible) to `1` (fully visible).

### 6.1 Basic usage

```css
.disabled-btn {
  opacity: 0.5; /* looks "inactive" */
  cursor: not-allowed;
  pointer-events: none; /* WHY: opacity alone does NOT disable clicks */
}

.image-dim {
  opacity: 0.8;
}
```

### 6.2 Important behaviors

1. **It affects children.** Child elements cannot be more opaque than their parent.
2. **`opacity: 0` is still clickable** and still takes up space. The element is invisible, not removed.
3. **`opacity < 1` creates a new stacking context** (see section 2.3).

```css
/* Faded background, but readable text: */

/* ❌ Wrong: the text fades too */
.banner-bad {
  background: black;
  opacity: 0.5;
  color: white;
}

/* ✅ Right: only the background is transparent */
.banner-good {
  background: rgb(0 0 0 / 0.5); /* alpha channel affects ONLY the background */
  color: white; /* text stays 100% opaque */
}
```

### 6.3 `opacity` vs `visibility` vs `display`

| Property             | Visible? | Takes space? | Clickable? | Animatable? |
| -------------------- | :------: | :----------: | :--------: | :---------: |
| `opacity: 0`         |    No    |     Yes      |  **Yes**   |     Yes     |
| `visibility: hidden` |    No    |     Yes      |     No     |  Discrete   |
| `display: none`      |    No    |    **No**    |     No     |     No      |

### 6.4 Fade-in/out pattern

```css
.toast {
  opacity: 0;
  visibility: hidden; /* WHY: removes it from interaction and from screen readers once faded out */
  transform: translateY(10px);
  transition:
    opacity 0.3s ease,
    transform 0.3s ease,
    visibility 0s linear 0.3s;
}

.toast.show {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
  transition-delay: 0s;
}
```

### 6.5 Hover dimming of siblings

```css
.gallery:hover img {
  opacity: 0.5;
} /* dim everything when hovering the gallery… */
.gallery img:hover {
  opacity: 1;
} /* …except the image you're pointing at */
.gallery img {
  transition: opacity 0.25s;
}
```

---

## 7. Overflow

`overflow` decides **what happens when content is bigger than its container**.

| Value     | Behavior                                         | Scrollbar?  |
| --------- | ------------------------------------------------ | ----------- |
| `visible` | Content spills outside (default)                 | No          |
| `hidden`  | Extra content is clipped                         | No          |
| `scroll`  | Always clipped, scrollbars always shown          | Always      |
| `auto`    | Clipped, scrollbars **only when needed**         | When needed |
| `clip`    | Like `hidden`, but not scrollable even by script | No          |

You can control each axis separately: `overflow-x` and `overflow-y`.

### 7.1 `visible` (default)

```css
.box {
  width: 200px;
  height: 60px;
  border: 1px solid #aaa;
  overflow: visible; /* long text will hang out of the box and may overlap neighbours */
}
```

### 7.2 `hidden`

Clips overflow. It has three common uses:

```css
/* 1. Rounded image corners: the image would otherwise poke out of the rounded frame */
.avatar-frame {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  overflow: hidden;
}
.avatar-frame img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* 2. Zoom-on-hover inside a fixed frame */
.thumb {
  overflow: hidden;
} /* the frame clips the enlarged image */
.thumb img {
  transition: transform 0.4s ease;
}
.thumb:hover img {
  transform: scale(1.1);
}

/* 3. Containing floats / margin collapse (creates a block formatting context) */
.row {
  overflow: hidden;
}
```

### 7.3 `auto` and `scroll`

```css
/* Scrollable chat history with a fixed height */
.chat-log {
  height: 300px;
  overflow-y: auto; /* WHY auto: no ugly empty scrollbar when there are only a few messages */
  overflow-x: hidden; /* prevent sideways scrolling */
}

/* Wide table that scrolls horizontally on phones instead of breaking the layout */
.table-wrap {
  overflow-x: auto;
}
.table-wrap table {
  min-width: 700px;
}
```

```css
/* Smooth, predictable scrolling behavior */
.chat-log {
  scroll-behavior: smooth;
  overscroll-behavior: contain; /* WHY: stops the page behind from scrolling when you hit the end of the chat */
}
```

### 7.4 Text truncation with ellipsis

```css
/* Single line: three properties are needed together */
.title {
  white-space: nowrap; /* 1. keep the text on one line */
  overflow: hidden; /* 2. clip what doesn't fit */
  text-overflow: ellipsis; /* 3. show "…" at the cut-off point */
  max-width: 240px;
}

/* Multi-line: clamp to 3 lines */
.excerpt {
  display: -webkit-box;
  -webkit-line-clamp: 3; /* limit to 3 lines */
  -webkit-box-orient: vertical;
  overflow: hidden; /* required for the clamp to take effect */
}
```

### 7.5 Interaction with `position`

- An `absolute` child inside a parent with `overflow: hidden` **gets clipped**, **if** that parent (or a positioned ancestor between them) is the child's containing block or sits inside it.
- `overflow: hidden/auto` on an ancestor **breaks `position: sticky`** for its descendants, because that ancestor becomes the scroll container.

```css
/* Dropdown clipped by a card? Move overflow: hidden to an inner wrapper
   instead of the element that contains the dropdown. */
.card {
  position: relative;
} /* the dropdown can now extend outside */
.card-media {
  overflow: hidden;
  border-radius: 12px;
} /* only the image is clipped */
```

---

## 8. Shadows (text & box)

### 8.1 `text-shadow`

**Syntax:** `text-shadow: <x-offset> <y-offset> <blur> <color>;`

| Part     | Meaning                                 |
| -------- | --------------------------------------- |
| x-offset | Horizontal shift (positive = right)     |
| y-offset | Vertical shift (positive = down)        |
| blur     | Optional. Higher = softer (default `0`) |
| color    | Shadow color                            |

```css
/* Subtle shadow to lift text off a busy photo */
.hero-title {
  color: #fff;
  text-shadow: 0 2px 6px rgb(0 0 0 / 0.6); /* WHY: no x offset → even shadow; soft blur → natural look */
}

/* Hard retro shadow (blur = 0 gives a solid copy) */
.retro {
  color: #ffd166;
  text-shadow: 3px 3px 0 #ef476f;
}

/* Glowing neon effect: stacked shadows, from tight to wide */
.neon {
  color: #fff;
  text-shadow:
    0 0 4px #fff,
    /* bright core */ 0 0 12px #0ff,
    /* inner glow */ 0 0 28px #0ff,
    /* outer glow */ 0 0 48px #08f; /* far halo */
}

/* Letterpress / embossed look on light backgrounds */
.letterpress {
  color: #555;
  background: #ddd;
  text-shadow: 0 1px 0 rgb(255 255 255 / 0.8); /* a light 1px edge below suggests the text is pressed in */
}
```

### 8.2 `box-shadow`

**Syntax:** `box-shadow: [inset] <x> <y> <blur> <spread> <color>;`

| Part    | Meaning                                                      |
| ------- | ------------------------------------------------------------ |
| `inset` | Optional. Draws the shadow **inside** the box                |
| x, y    | Offsets                                                      |
| blur    | Softness of the edge                                         |
| spread  | Optional. Positive = grows the shadow, negative = shrinks it |
| color   | Shadow color (use semi-transparent for realism)              |

```css
/* Soft "elevated card": the most common modern shadow */
.card {
  box-shadow: 0 4px 12px rgb(0 0 0 / 0.1); /* low opacity + blur = natural, not heavy */
}

/* Layered shadows look more realistic than a single one */
.card-realistic {
  box-shadow:
    0 1px 2px rgb(0 0 0 / 0.08),
    /* tight contact shadow */ 0 8px 16px rgb(0 0 0 / 0.1),
    /* mid-range depth */ 0 24px 48px rgb(0 0 0 / 0.12); /* large ambient shadow */
}

/* Negative spread: shadow only on the bottom edge */
.bottom-only {
  box-shadow: 0 12px 10px -10px rgb(0 0 0 / 0.4);
  /* WHY: the -10px spread shrinks the shadow so it doesn't show on the sides */
}

/* Inset: pressed button or inner field */
.input {
  box-shadow: inset 0 2px 4px rgb(0 0 0 / 0.15); /* looks sunken into the page */
}

/* Focus ring that doesn't affect layout (unlike border) */
.input:focus {
  outline: none;
  box-shadow: 0 0 0 3px rgb(45 108 223 / 0.35); /* 0 blur + 3px spread = a crisp ring */
}

/* Fake border without adding to the element's size */
.tag {
  box-shadow: 0 0 0 1px #ccc; /* behaves like a 1px border but doesn't change dimensions */
}
```

### 8.3 Shadow comparison

| Need                            | Use                        | Why                                         |
| ------------------------------- | -------------------------- | ------------------------------------------- |
| Shadow behind text              | `text-shadow`              | Follows letter shapes                       |
| Shadow around a rectangular box | `box-shadow`               | Follows the box (and its `border-radius`)   |
| Shadow around a PNG/SVG outline | `filter: drop-shadow(...)` | Follows the **visible pixels**, not the box |

```css
/* box-shadow would draw a rectangle around a transparent logo; drop-shadow hugs the shape */
.logo {
  filter: drop-shadow(0 4px 6px rgb(0 0 0 / 0.3));
}
```

### 8.4 Animating shadows

```css
.btn {
  box-shadow: 0 2px 4px rgb(0 0 0 / 0.2);
  transition:
    box-shadow 0.2s ease,
    transform 0.2s ease;
}

.btn:hover {
  box-shadow: 0 8px 20px rgb(0 0 0 / 0.25);
  transform: translateY(-2px);
}
.btn:active {
  box-shadow: 0 1px 2px rgb(0 0 0 / 0.3);
  transform: translateY(0);
} /* "pressed" feeling */
```

> **Performance note:** animating `box-shadow` is more expensive than `transform`/`opacity`. For many elements, put the final shadow on a `::after` pseudo-element and fade its `opacity` instead.
