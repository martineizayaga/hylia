---
title: 'Game Boy Implementation'
date: '2020-05-15'
tags:
  - CSS
  - TIL
  - Dribbble
---
## Background

Recently I was on Dribble and I found [this design](https://dribbble.com/shots/11303806-Figma-Smart-Animated-Gameboy) by [Rogie](https://dribbble.com/rogie). I had read somewhere on the web that implementing designs from Dribble was a good way to sharpen CSS skills. So I figured, why not, I would have at it.

## Finished Product
<video autoplay loop muted controls controlslist="nodownload nofullscreen noremoteplayback" preload="auto" style="border: 1px solid black">
  <source src="/videos/gameboy-demo.mp4" type="video/mp4">
  <source src="/videos/gameboy-demo.webm" type="video/webm">
  Your browser doesn't support the HTML5 video tag :(
</video>

<p class="codepen" data-height="550" data-theme-id="light" data-default-tab="result" data-user="martineizayaga" data-slug-hash="gOaeOgK" style="height: 550px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Gameboi">
  <span>See the Pen <a href="https://codepen.io/martineizayaga/pen/gOaeOgK">
  Gameboi</a> by Martín E (<a href="https://codepen.io/martineizayaga">@martineizayaga</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## How I Made It
I thought that this was going to be very straightforward, but I was surprised at the number of challenges that came my way and how much I would learn.

### Stacked Box Shadows
The first challenge was trying to replicate the shadow. It wasn't a challenge at all because of [Sara L. Fossheim's blog post on box-shadows](https://fossheim.io/writing/posts/css-box-shadow-animation/). I had no idea that one could stack several box-shadows.
```css
#gameboy {
  box-shadow:
    15px 0px 0 0px black,
    20px 0px 0 0px white,
    32px 0px 0 0px black;
}
```

To make the shadow horizontally interactive, I wrote a function that changed the `box-shadow` of `#gameboy` every time the slider value changed.

```javascript
function changeShadow(value) {
  gameboy.style.boxShadow = `
    ${15 * value / 100}px 0px 0 0px black,
    ${20 * value / 100}px 0px 0 0px white,
    ${32 * value / 100}px 0px 0 0px black
  `;
}
```

The  `value` goes from -100 to 100, so the `h-offset` so the first `box-shadow`, for instance, would go from -15 to 15. Perfect 😎.

The box-shadows for the other parts of the Game Boy (like the buttons) were manipulated in pretty much the same way.

### The Inner Face Illusion
One of the disadvantages of using `box-shadow` was that I could not figure out a way to have the face be a part of the shadow (if you know, let me know pls!) So, I had to figure out how to give the *illusion* of there being a box cutout where the screen is.

I started by making a black `div` with the face on it, but the it covered the outer screen's bottom right border radius. After much Googling, I [found out](https://stackoverflow.com/a/11725743) about `display: hidden`. It's when the overflow content is clipped.

In order to have it shift left and right with the horizontal slider, I added a line to the `changeShadow` function.

```javascript
  screenBlack.style.left = `${value/10}px`;
```

Since the black screen had an absolute positioning, a negative `left` positioning would shift it out of the parent screen. The `value` goes from -100 to 100, so the `left` property would go from -10 to 10.

### Advanced Border Styling
I had to round the bottom right corner of the Game Boy, but `border-radius: value` didn't suffice. I found out that one can specify the length of the curve on every edge of the border. Here's what I had for `#gameboy`.
```css
#gameboy {
  ...
  border-top-left-radius: 5% 3%;
  border-top-right-radius: 5% 3%;
  border-bottom-right-radius: 25% 15%;
  border-bottom-left-radius: 5% 3%;
  ...
}
```

This would mean, for instance, that the first value of `border-bottom-right-radius` is for the bottom border and the second value is for the right border.

I used this trick to make the mouth.

```css 
.mouth {
  ...
  border-radius: 50% 50% 50% 50% / 0% 0% 70% 70%;
  ...
}
```

This is shorthand for the following.

```css
border-top-left-radius: 50% 0%;
border-top-right-radius: 50% 0%;
border-bottom-right-radius: 50% 70%;
border-bottom-left-radius: 50% 70%;

```

### Styling the Sliders
The last big challenge of this implementation was prettying up the sliders. The style that [Rogie](https://dribbble.com/rogie) chose was similar to the shadow of the Game Boy, so I couldn't just leave the default sliding styles. Little did I know of how picky each browser woudl be with slider styles. I found a great [CSS-Tricks article](https://css-tricks.com/styling-cross-browser-compatible-range-inputs-css/) that really helped me out.

Safari and Firefox have their own CSS selectors for the track (the sliding track) and the thumb (what you click to drag to a value.) There's not much more than I can say than to recommend the [CSS-Tricks article](https://css-tricks.com/styling-cross-browser-compatible-range-inputs-css/).

I did however find out that an `<input>` with `type="range"` and `orient="vertical"` won't have css styles in Webkit (Chrome, Safari, Opera.) So I had to rotate the input instead. I was kind of sad about that though. I like adding new HTML attributes if they're helpful :(.

```css
#verticalRange {
  grid-area: vslider;
  -webkit-appearance: none;
  transform: rotate(-90deg);
}
```

In order to stay true to the design, I had the thumb slider have a box-shadow and a border the same width as the rest of the design. Here's what I had for WebKit.

```css
input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none;
  border: var(--border-width) solid black;
  height: 20px;
  width: 20px;
  border-radius: 50%;
  background-color: white;
  cursor: pointer;
  box-shadow: 0px 2px 0 0px black;
  box-sizing: border-box;
}
```

## What I learned
- `box-shadow's` can be stacked.
- `overflow: hidden` clips the content that overflows.
- Browsers have different pseudo selectors for input sliders and thumbs.
- WebKit input sliders won't show styling if it has `orientation="vertical"`. You should probably rotate the slider instead.
- `border-radius` can have more than one value and has a handy shorthand.
- Implementing designs from Dribble is kinda fun :).
