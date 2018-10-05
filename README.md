# Viewport breakpoint font sizer
A Sass function to facilitate predictable viewport-based font sizes.

This Sass function uses simple math to figure out what combination of settings would yield the desired font size at the breakpoint where you want the switch between increasing upward and increasing downward to occur.

Sass functions are written in simple .scss files, and they can be placed anywhere -- I stick them in their own functions directory, and I call them pretty early in my Sass chain, right after I declare my variables. 

## Demo
You can see it in action at CodePen: https://codepen.io/bixgomez/pen/wYWZVY

## Requirements
You will need to download and deploy Hugo Giraudel's "strip-units" function in order for this to work:

https://css-tricks.com/snippets/sass/strip-unit-function/

(I have included it in this repo in case it should disappear from css-tricks.)

## Usage
Let's say we want the base font size to be 14px, and that we want the text to be that size at the 800px breakpoint.  Let's say we have also decided that we want the font size to increase at a rate of 1.5vw on the way "up" (wider than 800px), and at a rate of 1.0vw on the way "down" towards mobile width.

Our Sass, therefore, would be as follows:

```
// The size you want your font to be at the "switch point"
$threshold-px: 14px;

// The breakpoint at which you want yout font size to be $threshold-px
$switch-point: 800px;

// The rate that you want font size to increase as browser width increases from $switch-point
$vw-rate-up: 1.5vw;

// The rate that you want font size to increase as browser width decreases from $switch-point
$vw-rate-down: 1.0vw;

$up-size: baseFontSize($threshold-px, $switch-point, $vw-rate-up, 'add');
$down-size: baseFontSize($threshold-px, $switch-point, $vw-rate-down, 'subtract');

.your-container {
  font-size: calc( #{$down-size} - #{$vw-rate-down} );

  @include breakpoint($switch-point) {
    font-size: calc( #{$up-size} + #{$vw-rate-up} );
  }

  @include breakpoint($max-site-width) {
    font-size: calc( #{$up-size} + #{$max-site-width/100 * strip-unit($vw-rate-up)} );
  }
}
```

The Sass function receives the four values we're sending it, and the first thing it does is strip the units from the numerical arguments, using a function I found at css-tricks.com called strip-unit.

Next, it makes a fairly simple calculation -- It multiplies the breakpoint by the viewport factor, or 800 x 1.75, then divides that value by 100 to give us 14, which is 1.75vw's value in pixels when the viewport is 800px wide.

Finally, we either add or subtract this value from the desired value in pixels (depending on whether we are going up or down), and that magically gives us the value that we then add or subtract our vw value back in our original Sass.
