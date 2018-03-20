# Viewport breakpoint font sizer
A Sass function to facilitate predictable viewport-based font sizes.

## Requirements
You will need to download the "strip-units" function in order for this to work:

https://css-tricks.com/snippets/sass/strip-unit-function/

## Usage

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
