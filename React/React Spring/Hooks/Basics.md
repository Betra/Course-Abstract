# Basics

There are 5 hooks at the time:

* `useSpring`: a single spring, moves data from a -> b
* `useSprings`: multiple springs, for lists, where each spring moves data from a -> b
* `useTrail`: one spring follows or trails behind the other
* `useTransition`: for mount/unmount transitions
* `useChain`: to queue or chain multiple animations together

The easiest is this:

```js
import { useSpring, animated } from 'react-spring'

function App() {
  const props = useSpring({ opacity: 1, from: { opacity: 0 } })
  return <animated.div style={props}>I will fade in</animated.div>
}
```

### First step — **fetch your imports**

```js
import { useSpring, animated } from 'react-spring'
```

Besides primitive you need `animated`. It animates outside of React. Your view has to know how to handle the animated props you pass to it. This is what animated is there for, it extends native elements to receive animated values.

### Second step — **define your spring**

```js
const props = useSpring({ opacity: 1, from: { opacity: 0 } })
```

Spring animates from one state to the next. The received props are not static values! These props are self-updating, you cannot use them in regular divs and such.

### Third step — **tie the animated value to your view**

```js
return <animated.div style={props}>I will fade in</animated.div>
```

If your target is the web then animated contains all valid html elements (divs, spans, svgs, etc). If you want to animate React components, styled-components, or elements on other platforms, then do this:

```js
// React components
const AnimatedDonut = animated(Donut)

// React-native
const AnimatedView = animated(View)

// styled-components, emotion, etc.
const AnimatedHeader = styled(animated.h1)`
  ...
```

**Values you pass are not styles**; although you can use them as such.

## Up-front interpolation

Springs take pretty much everything:

* Colors (names, rgb, rgba, hsl, hsla, gradients)
* Absolute lengths (cm, mm, in, px, pt, pc)
* Relative lengths (em, ex, ch, rem, vw, vh, vmin, vmax, %)
* Angles (deg, rad, grad, turn)
* Flex and grid units (fr, etc)
* All HTML attributes
* SVG paths (as long as the number of points matches, otherwise use custom interpolation)
* Arrays
* String patterns (transform, border, boxShadow, etc)
* Non-animatable string values (visibility, pointerEvents, etc)
* ScrollTop/scrollLeft

```js
const props = useSpring({
  vector: [0, 10, 30],
  display: 'block',
  padding: 20,
  background:
    'linear-gradient(to right, #009fff, #ec2f4b)', 
  transform: 
    'translate3d(0px,0,0) scale(1) rotateX(0deg)', 
  boxShadow: '0px 10px 20px 0px rgba(0,0,0,0.4)', 
  borderBottom: '10px solid #2D3747', 
  shape: 'M20,20 L20,380 L380,380 L380,20 L20,20 Z', 
  textShadow: '0px 5px 15px rgba(255,255,255,0.5)',
})
```

## View interpolation

You may wonder why you wouldn't always interpolate inside of the spring. View interpolation can be a little faster, and it takes up less space.

```js
import { useSpring, animated, interpolate } from 'react-spring'

const { o, xyz, color } = useSpring({
  from: { o: 0, xyz: [0, 0, 0], color: 'red' },
  o: 1, xyz: [10, 20, 5], color: 'green'
})

return (
  <animated.div
    style={{
      // If you can, use plain animated values like always, ...
      // You would do that in all cases where values "just fit"
      color,
      // Unless you need to interpolate them
      background: o.interpolate(o => `rgba(210, 57, 77, ${o})`),
      // Which works with arrays as well
      transform: xyz.interpolate((x, y, z) => `translate3d(${x}px, ${y}px, ${z}px)`),
      // If you want to combine multiple values use the "interpolate" helper
      border: interpolate([o, color], (o, c) => `${o * 10}px solid ${c}`),
      // You can also form ranges, even chain multiple interpolations
      padding:
        o.interpolate({ range: [0, 0.5, 1], output: [0, 0, 10] })
         .interpolate(o => `${o}%`),
      // Interpolating strings (like up-front) through ranges is allowed ...
      borderColor: o.interpolate({ range: [0, 1], output: ['red', '#ffaabb'] }),
      // There's also a shortcut for plain, optionless ranges ...
      opacity: o.interpolate([0.1, 0.2, 0.6, 1], [1, 0.1, 0.5, 1]),
  }}>
    {o.interpolate(n => n.toFixed(2)) /* innerText interpolation ... */ }
  </animated.div>
)
```

## Additional Notes

### Animating `auto`

Handling "auto" is out of scope of the hooks api, as they don't know the view

```js
const [bind, { height }] = useMeasure()
const props = useSpring({ height })
return (
  <animated.div style={{ overflow: 'hidden', ...props }}>
    <div {...bind}>content</div>
  </animated.div>
)
```

### Emulating css-frames

Sometimes you want to go through a series of states, like css keyframes, you can use interpolations for this.

```js
/*
0 % { transform: scale(1); }
25 % { transform: scale(.97); }
35 % { transform: scale(.9); }
45 % { transform: scale(1.1); }
55 % { transform: scale(.9); }
65 % { transform: scale(1.1); }
75 % { transform: scale(1.03); }
100 % { transform: scale(1); }
`*/

const props = useSpring({ x: state ? 1 : 0 })
return (
  <animated.div
    style={{
      transform: props.x
        .interpolate({
          range: [0, 0.25, 0.35, 0.45, 0.55, 0.65, 0.75, 1],
          output: [1, 0.97, 0.9, 1.1, 0.9, 1.1, 1.03, 1]
        })
        .interpolate(x => `scale(${x})`)
    }} />
)
```