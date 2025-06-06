stac-auth-proxy <!-- .element: class="r-fit-text" -->

---

anthony lukach

cloud engineer@developmentseed

---

## why?

auth story is still unsolved for stac

---

<small>why...</small>

many stac backends

---

<small>why...</small>


many auth strategies

---

<small>why...</small>

varied auth needs

--

<small>why... (auth needs)</small>


entirely private catalogs

--

<small>why... (auth needs)</small>

public catalogs, rectricted edits

NOTE: useful for data editors who need to make one-off edits to a record

--

<small>why... (auth needs)</small>

subset of data is private

NOTE: i.e. private collections / items

--

<small>why... (auth needs)</small>

asset access

NOTE: 

typically, STAC is merely for the discovery of assets

it is the assets themselves that matter

how does we marry asset access policies with our STAC access policies?

---

## what?

auth _before_ your stac api

--

not solving everyones needs _(yet)_

targetting oidc compliant auth servers

---

Go Big<!-- .element: class="r-fit-text" -->

---

Press <kbd>ESC</kbd> to see overview

--

Press <kbd>S</kbd> to open speaker view.

You can even include notes that only the presenter can see!

<!-- Presenter notes are specified by anything written below a line that starts with "NOTE: " -->

NOTE: This is only visible to the presenter.

Drag this tab to a different window or screenshare the other tab. When you change slides on this presenter-view tab, the other tab with audience focused tabs will change as well.

--

Press <kbd>?</kbd> to see other keyboard shortcuts.

---

## Fragment transitions

Fade in <!-- .element: class="fragment" -->

Fade out <!-- .element: class="fragment fade-out" -->

Highlight red <!-- .element: class="fragment highlight-red" -->

Fade in, then out <!-- .element: class="fragment fade-in-then-out" -->

Slide up while fading in <!-- .element: class="fragment fade-up" -->

NOTE: See more about fragment transitions here: https://revealjs.com/fragments/

---

<!-- .slide: data-auto-animate -->

## Code

```js[|4|4,8-9|15,19-20]
import React, { useState } from "react";

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

function SecondExample() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

---


<!-- .slide: data-background-image="https://images.pexels.com/photos/31202661/pexels-photo-31202661/free-photo-of-spiral-staircase-at-vatican-museum-rome.jpeg?auto=compress&dpr=10" -->

## Backgrounds

Images as backgrounds

NOTE: Could be relative path to images as well...

--

<!-- .slide: data-background-gradient="linear-gradient(to bottom, #283b95, #17b2c3)" -->

## Backgrounds

Linear gradient background 🐟

--

<!-- .slide: data-background-gradient="radial-gradient(#283b95, #17b2c3)" -->

## Backgrounds

Radial gradient background 🐳

--

<!-- .slide: data-background-video="https://static.slid.es/site/homepage/v1/homepage-video-editor.mp4" data-background-video-loop data-background-video-muted -->

## Backgrounds

Video background

--

<!-- .slide: data-background-iframe="https://semver.org" data-background-interactive -->

## Backgrounds

Websites as backgrounds

NOTE: This doesn't currently work with any page that prevents itself from being included in iframes (e.g. GitHub)
