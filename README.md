# Viewport breakpoint font sizer
This is a Sass function (and mixin) to facilitate predictable viewport-based font sizes.

It uses relatively simple math to deliver the combination of settings that will give us the font size we want at a specific "threshold" breakpoint, and variable font sizes "above" and "below" this point at specified rates of increase and decrease.

## Demo
You can see it in action at CodePen: https://codepen.io/bixgomez/pen/wYWZVY

## Dependencies
You will need to install Hugo Giraudel's "strip-units" function in order for this to work:

https://css-tricks.com/snippets/sass/strip-unit-function/

(I have included it in this repo in case it should disappear from css-tricks.)

## Variables used in both function and mixin

* ***$threshold-px***
  * The size you want your font to be at the "switch point". This can be considered your "baseline" font size, as it will never get smaller than this.
  * Default value: 14px
  
* ***$switch-point***
  * The breakpoint, in pixels, at which you want your font size to be $threshold-px.
  * Default value: 850px
* ***$vw-rate-up***
  * The rate that you want font size to increase as browser width increases from $switch-point. The higher the value, the larger the font will grow as the browser increases in width. A negative value will decrease the font size. A value of 0vw will disable the font from either growing or shrinking.
  * Default value: 1.5vw
* ***$vw-rate-down***
  * The rate that you want font size to increase as browser width decreases from $switch-point. The higher the value, the larger the font will grow as the browser decreases in width. As above, a negative value will decrease the font size, and a value of 0vw will disable the font from either growing or shrinking.
  * Default value: 1.125vw
* ***$stop-point***
  * The browser size at which you want this effect to "stop" -- when you'd like your fonts to stop growing. Can use any unit, including percents (so you can use 100% if you like).
  * Default value: 1400px

## Usage (function)
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

The function receives the four values we're sending it, and the first thing it does is strip the units from the numerical arguments, using a function I found at css-tricks.com called strip-unit.

Next, it makes a fairly simple calculation -- It multiplies the breakpoint by the viewport factor, or 800 x 1.75, then divides that value by 100 to give us 14, which is 1.75vw's value in pixels when the viewport is 800px wide.

Finally, we either add or subtract this value from the desired value in pixels (depending on whether we are going up or down), and that magically gives us the value that we then add or subtract our vw value back in our original Sass.

## Usage (mixin)

At its most basic, all you need is to include the viwwport-font-sizer mixin. This will provide the baseline functionality, using the default values as listed above.

```
@include viwwport-font-sizer;
```

If you wish to adjust any of the settings or font size values, these can be passed in any order to the mixin as follows:

```
@include viwwport-font-sizer(
  $threshold-px: 16px,
  $switch-point: 768px
);
```
