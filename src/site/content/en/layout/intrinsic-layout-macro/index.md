---
layout: post
title: Macro Layout
authors:
  - adamargyle
description:
web_lighthouse: N/A
date: 2019-03-05
codelabs:
  - codelab-intrinsic-layout-macro-v1
  - codelab-intrinsic-layout-macro-v2
---

{% Aside 'key-term' %}
  Macro layouts are the top most layouts of a page. Think of it like **the white space around your white space** or **the forest's layout rather than a tree's layout**.
{% endAside %}


## &#60;Discovery-Phase&#62;
I find overlaying estimated macro grid tracks and areas on a design pacify the initially overwhelming nature of starting from scratch. I use my wrong initial assumptions to fail fast where it's cheap to retry. I'm not drawing tracks to inform my code, I'm drawing tracks to inform my strategy.

**I don't expect my groupings to be right, but I expect it to help me get to the right solution faster.** It's a discovery phase where I'm learning the relationships between elements by grouping them (probably wrong), and adjusting.

<figure class="w-figure w-figure--fullbleed">
  <picture>
    <source type="image/jpeg" srcset="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-initial-estimate%402x.jpg 2x"/>
    <img loading="lazy" src="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-initial-estimate.jpg" alt="Full page design mockup with estimated rows and columns highlighted" class="screenshot">
  </picture>
</figure>

{% Aside 'objective' %}
  After drawing a few estimated gridlines and getting more familiar with their relationship, I'm ready to attempt some assertions. I've got semantic assertions, some observation assertions, as well as some preliminary concerns to be mindful of as we continue.
{% endAside %}

<br>

#### Layout Semantics
Goal here is to identify HTML elements that match the intent alluded to by the design. For me it's a typical translation task, where I'm translating elements and interactions from the pixels to the web, aka the HTML provided base elements that serve those purposes.

- 2 `<nav>`s': 1 at the top, 1 on the side
- A `<main>` should be used to wrap an `<aside>` and an `<article>`
- The `<aside>` should contain a `<nav>`
- Lots of `<img>`'s inside `<figure>`'s' with `<figcaption>`'s
- `<input type="search"/>` in the top bar
- `<button>`s and `<a>`s

<br>

#### Layout Observations
These are places for me to aknowledge the consistencies and inconsistencies being presented by the design. These help inform my decisions as well as my refactors. It's also fun!

<figure class="w-figure--center" style="margin-bottom:5rem;">
  <picture>
    <source type="image/jpeg" srcset="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-concerning-estimate%402x.jpg 2x"/>
    <img loading="lazy" src="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-concerning-estimate.jpg" alt="problem area exposed" class="screenshot">
  </picture>
  <figcaption class="w-figcaption">
    These are the isolated overlapping zones I found when estimating tracks which expose some of the tricky parts we'll need to deal with
  </figcaption>
</figure>

<figure class="w-figure--center" style="margin-bottom:5rem;">
  <picture>
    <source type="image/jpeg" srcset="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-rag-right%402x.jpg 2x"/>
    <img loading="lazy" src="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-rag-right.jpg" alt="rag right grid shown" class="screenshot">
  </picture>
  <figcaption class="w-figcaption">
    An <b>asymmetric</b> layout, almost like the whole layout is aligned left and <a href="https://www.fonts.com/content/learning/fontology/level-2/making-type-choices/justified-vs-rag-right" target="_blank">rag right</a>
  </figcaption>
</figure>

<figure class="w-figure--center" style="margin-bottom:3rem;">
  <picture>
    <source type="image/jpeg" srcset="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-flanking-rails%402x.jpg 2x"/>
    <img loading="lazy" src="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-flanking-rails.jpg" alt="common edges highlighted" class="screenshot">
  </picture>
  <figcaption class="w-figcaption">
    <b>Rails appear to flank</b> a large flexible column
  </figcaption>
</figure>

<figure class="w-figure--center" style="margin-bottom:3rem;">
  <picture>
    <source type="image/jpeg" srcset="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-extrinsic-estimate%402x.jpg 2x"/>
    <img loading="lazy" src="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-extrinsic-estimate.jpg" alt="common edges highlighted" class="screenshot">
  </picture>
  <figcaption class="w-figcaption">
    <b>Extrinsicly sized</b> rails and sidebar space
  </figcaption>
</figure>

<figure class="w-figure--center" style="margin-bottom:3rem;">
  <picture>
    <source type="image/jpeg" srcset="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-split%402x.jpg 2x"/>
    <img loading="lazy" src="https://storage.googleapis.com/web-dev-assets/intrinsic-layout-macro/macro-split.jpg" alt="common edges highlighted" class="screenshot">
  </picture>
  <figcaption class="w-figcaption">
    Shipping message is splitting our potentially shared tracks
  </figcaption>
</figure>

<br>

#### Layout Concerns
1. Our side `<aside>` and site name share the same column, but they're in different places in the DOM tree
1. `<nav>` and `<main>` share column guides but we don't have [subgrid](https://rachelandrew.co.uk/archives/2018/04/27/grid-level-2-and-subgrid/) to enforce or enable shared tracks
1. The rails will need to collapse on mobile changing alignments of the account and brand logo

<br><br>

#### Process Recap
I don't have a systemic process to share in regards to these assertions. I look for alignments, shared spaces, dynamic content areas, and I try to see which areas **should** be flexible. Top that off with an intent to **let the content speak for itself** when it can with a [content outward](https://alistapart.com/article/content-out-layout/) layout strategy, let [content length be a breakpoint](http://bradfrost.com/blog/post/7-habits-of-highly-effective-media-queries/#content).

Even if my first layout works great, I like going back and looking for places to optimize: trim logic, code, or whatever. **Just like I do in my Javascript.**

## &#60;/Discovery-Phase&#62;


<br><br><br>


###### Let's Code!
## Macro Layout v1: A Slotted Layout
{% Aside 'objective' %}
  1 grid to rule them all 💍
{% endAside %}

In this iteration, we follow our gut and do our best to articulate one grid to handle this macro layout. It looks like we can do **the whole grid with 1 definition using a slotted layout**. We have identifiable elements and grid lines, so let's translate them to [grid-template-areas](<https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-areas>), **place** our elements in the **defined zones**, and go from there.

<a class="w-button w-button--primary w-button--with-icon" data-icon="code" href="/codelab-intrinsic-layout-macro-v1">
  Codelab: Slotted Macro Layout
</a>

<!-- {% Aside 'codelab' %}
  [Slotted Macro Layout](/codelab-intrinsic-layout-macro-v1)
{% endAside %} -->

<br><br><br>

###### Let's Code!
## Macro Layout v2 (refactor): Intrinsic Grids
{% Aside 'objective' %}
  2 grids that do less
{% endAside %}

In this refactor, I pulled the `<header>` element out. Upon a closer look, I noticed that the `<header>` didnt need to be in a grid at all! It's a full width element from biggest screens to smallest. **Felt like I overarchitected the first grid** / incurred code debt to be pushing that naturally responsive element into a more complicated layout.

Post refactor, 1 grid manages the "stack" and the 2nd manages the aside and articles.

<a class="w-button w-button--primary w-button--with-icon" data-icon="code" href="/codelab-intrinsic-layout-macro-v2">
  Codelab: Intrinsic Macro Grids
</a>

<!-- {% Aside 'codelab' %}
  [Intrinsic Macro Grids](/codelab-intrinsic-layout-macro-v2)
{% endAside %} -->




<!-- <br><br><br><br>

## Responsive Final Touches
TODO: move into codelabs

Due to our nice refactor, **our responsive work is pretty minimal**. Nothing needs to be changed on the `<body>`, woh, love it, and the `<main>` grid **only needs a minor adjustment**:

<br>

```css
@media (width < 768px) {
  grid-template-columns: 1fr;
  margin: 0 0 0 1rem;
}
```

<div class="note">
  <b>Plain Speak:</b> When the viewport width is less than 768px, I want a <b>grid</b> with <b>1 full width column</b> and margin only on the left side.
</div>

<figure style="text-align:center; margin: 4rem 0; max-width: 400px;">
  <img src="main-responsive.png" alt="Main grid simplified to 2 columns" class="screenshot">
</figure> -->






<br><br><br><br>

## Conclusion
The refactored v2 of our macro layout is less code, less assumptive and will scale well as new children get introduced. **We've relinquished some of our control over the content** by defining less areas with specific widths, and instead, defining containers mostly responsible for spacing and direction.

#### Results
- Container children can **change node names, position or length** and **no grid code needs modified** (aka less assumptive)
- Selectors **know less**
- Less `grid-template-columns`
- Less code in media queries
- Less to think about
- Less to manage

<!-- <br><br>

**Final Macro Layout**
<div class="glitch-embed-wrap" style="height: 346px; width: 100%;">
  <iframe
    src="https://glitch.com/embed/#!/embed/logical-tab-order?path=index.html&previewSize=100&attributionHidden=true"
    alt="logical-tab-order on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

<br><br>

**Play with that Glitch a bit!** Resize your browser. Use the devtools to visualize the grid spaces! -->

<br>

#### Did we match our estimated grid lines!? Nope! 😹

Turns out my original guesses on grid lines were off.. **what's new**! Here are the designs with **updated grid lines** based on the learnings from development which also accurately reflect the refactored results.

<br>

### Top Level Grid

```css
body {
  display: grid;
  gap: 2rem;
  grid-auto-flow: row;

  @media (width > 768px) {
    & > :matches(.greeting, main) {
      margin-left: var(--body-rails);
    }
  }
}
```

{% Compare 'better', 'Advantages' %}
Just flow/direction and spacing
{% endCompare %}

<figure class="w-figure w-figure--fullbleed">
  <img src="macro – body rows.png" alt="">
</figure>


<br><br>

### Main Level Grid

```css
main {
  display: grid;
  grid-template-columns: var(--sidebar-width) 1fr;

  @media (width <= 768px) {
    display: contents;
  }
}
```

{% Compare 'better', 'Advantages' %}
2 columns with margin, 1 extrinsic (`<aside>`) the other fluid
{% endCompare %}

TODO: update graphic to only be the main grid and not have the header. consider making margin a different color and being consistent with column visuals

<figure class="w-figure w-figure--fullbleed">
  <img src="macro – less grids more margin.png" alt="">
</figure>

<br><br>

### All Together

TODO: better gif of results

{% Compare 'better', 'Result' %}
Devtools highlighting the 2 grids and showing them respond from desktop to mobile
{% endCompare %}

<figure class="w-figure w-figure--fullbleed">
  <img src="intrinsic-macro-1.gif" alt="">
</figure>
