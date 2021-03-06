---
type: documentation
title: Animation
resources:
  links:
    name: "Debugging Chrome Animations"
    source: http://valhead.com/2015/01/06/quick-tip-chrome-animation-controls/
---


## Animation Guidelines

The overarching metaphor for our animation comes from the IBM Design Language metaphor *elegant machine motion*. Elegant machine motion consists of very quick movements with strong easing at the beginning and/or end of the animation, plus subtle offsets.


## Properties

Properties are the elements of an object that will change over time. The properties that can animate within CSS are listed below. We recommend using the most performant properties first. If the desired effect cannot be achieved, use the general list of properties as a fallback.

**Most Performant Properties**

* Position: `transform: translate(npx,npx);`
* Scale: `transform: scale(n);`
* Rotation: `transform: rotate(ndeg);`
* Opacity: `opacity: 0...1;`

For more information about why these properties are performant see [this blog post.](http://www.html5rocks.com/en/tutorials/speed/high-performance-animations/)

To see a full list of animatable properties and examples of them animating visit [Animatable.](http://leaverou.github.io/animatable/)


### Single Property Animations

When animating only a single property, follow the guidelines below. 

![Scaling of a box from 0% to 100%](images/motion/examples/Single_Attribute-1.gif)

```scss
//box class
.single-attribute {
  animation: single-attribute 1s map-get($timing-function, bounce-in);
}

//keyframes for animation
@keyframes single-attribute {
  0% {
    transform: scale(0);
  }
  100% {
    transform: scale(1);
  }
}
``` 

**Timing Function**

Using bounces eases to add the snap in factor that is lost if two properties are not used. These bounces are snappy bounces, not physics-based bounces.

  * Use of Timing Functions:
    * **Entrances** - `map-get($timing-function, bounce-in)` (Fast to Slow) 
    * **Exits** - `map-get($timing-function, bounce-out)` (Slow to Fast)
    * **Interactive Elements** - `map-get($timing-function, bounce-in-out)`

### Multiple Property Animations

Mutliple property animations are animations where multiple properties are being animated together. With multiple property animations there are two diretions on can take: Direction 1 - Start one property alone, then animate the additional properties, or Direction 2 - Both Properties start at the same time, then one property ends before the other. It is recommneded to not start and stop multiple properties at the same time; choose one or the other.

**Direction 1**

Start one property alone then animate the additional properties.

![Scaling box animation starting with X axis scale then the Y axis scale follows.](images/motion/examples/Option-A.gif)

```scss
//box class
.animation-a {
  animation: animation-a 1s map-get($timing-function, snap-in);
  transform-origin: 0% 100%;
}

//keyframes for animation
@keyframes animation-a {
  0% {
    transform: scale(0 , 0);
  }
  50% {
    transform: scale(.5 , 0);
  }
  100% {
    transform: scale(1 , 1);
  }
}
```

**Direction 2**

Both properties start at the same time then one property ends before the other.


![Scaling box animation starting with both X and Y axis scale then X axis ends before Y finishes.](images/motion/examples/Option-B.gif)

```scss
//box class
.animation-b {
  animation: animation-b 1s map-get($timing-function, snap-in);
  transform-origin: 0% 100%;
}

//keyframes for animation
@keyframes animation-b {
  0% {
    transform: scale(0 , 0);
  }
  30% {
    transform: scale(.2 , 0);
  }
  80% {
    transform: scaleY(.5);
  }
  100% {
    transform: scale(1 , 1);
  }
}
```

**Timing Function**

Using snap in eases addes very strong eases to quick movements which enhances the elegant machine motion metaphor.

* Use of Timing Functions: 
  * **Entrances** - `map-get($timing-function, snap-in)` (Fast to Slow) 
  * **Exits** - `map-get($timing-function, snap-out)` (Slow to Fast)
  * **Interactive Elements** - `map-get($timing-function, snap-in-out)`


## Singular vs. Sequence of Actions

Within user interfaces, there are instances where only one element moves, as well as instances when multiple elements create a sequence of actions. The guidelines for both kind of instances are listed below.


### Singular Action

A singluar action animation occurs when only one element on the screen is animated, and there are no other complementary elements.

![Singular action shows a box animating scaling horizontally then vertically](images/motion/examples/Option-B.gif)

### Sequence of Actions

A _sequence of actions_ animation is an animation in which multiple elements animation, typically a primary action then a secondary action that complements the primary action.

![Secondary action shows a box animating scaling horizontally then vertically with text animating up following the hertical scale](images/motion/examples/secondary-action.gif)

In the example above, the text animations and delay enhance the animation by following the lead of the primary action, which in this case is the scaling of the box.

#### Things to consider with Sequence of Actions

  * Choreography: Elements should have coordinate within the determined hierarchy.
  * Delays: Delays need to be consistent and should have the same rate across similar content.

## Animation Library Implementation

All of the animations live within an animation map. It looks a bit like this:

```scss 
$animations: (
  'fade-in': (
    0%: (
      opacity: 0
    ),
    100%: (
      opacity: 1
    )
  ),
  'slide-in--left' : (
    0%: (
      transform: translateY(-100%)
    ),
    100%: (
      transform: translateY(0)
    )
  ),
  'slide-in--right' : (
    0%: (
      transform: translateY(100%)
    ),
    100%: (
      transform: translateY(0)
    )
  )
);
```

The format of the map is (with everything between <> being a string:

```scss
$animations: (
  '<animation name>': (
    <keyframe%>: (
      <property>: <value>;
    ),
    <keyframe%>: (
      <property>: <value>;
    )
  )
);
```

In order to use any of these animations in a product, we have implemented an animate Sass mixin. All that is needed to include the animation on the element, by using the following syntax: `@include animate(<animation name>, <duration>, <timing function>)`. If duration and timing function are not specified, we have included the default values of 2s and ease-in.

For example, one can implement `@include animate('fade-in');` or `@include animate(fade-in, 3s, snap-in);`.

The current animations provided are:

Entrance Animations
- `fade-in`
- `fade-in--up`
- `fade-in--down`
- `fade-in--left`
- `fade-in--right`
- `slide-in--up`
- `slide-in--down`
- `slide-in--left`
- `slide-in--right`

Exit Animations
- `fade-out`
- `fade-out--up`
- `fade-out--down`
- `fade-out--left`
- `fade-out--right`
- `slide-out--up`
- `slide-out--down`
- `slide-out--left`
- `slide-out--right`

### Timing Functions

The current timing functions provided are:

- `ease-in`
- `ease-out`
- `bounce-out`
- `bounce-in`
- `bounce-in-out`
- `snap-in`
- `snap-out`
- `snap-in-out`

Timing functions are mathematical equations that creates a bezier curve which is: a line that defines the acceleration pattern on a graph. Bezier curves are often translated to keywords like: ease-in, ease-out, and ease-in-out. They are also referred to as “Motion Curves” or “Curves”.

