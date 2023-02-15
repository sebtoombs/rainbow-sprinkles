<h1 align="center">Rainbow Sprinkles 🧁</h1>

<p align="center"><i>Dynamic, theme-driven, style props for <a href="https://vanilla-extract.style" rel="nofollow">vanilla-extract</a></i></p>

<div align="center">

[![Release](https://img.shields.io/github/v/release/wayfair/rainbow-sprinkles?display_name=tag)](/packages/rainbow-sprinkles/CHANGELOG.md)
[![license: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-2.0-4baaaa.svg)](CODE_OF_CONDUCT.md)
[![Maintainer](https://img.shields.io/badge/Maintainer-Wayfair-7F187F)](https://wayfair.github.io)
[![All Contributors](https://img.shields.io/github/all-contributors/wayfair/rainbow-sprinkles?color=ee8449&style=flat-square)](#contributors)

</div>

Rainbow sprinkles works similarly to `@vanilla-extract/sprinkles`. Like sprinkles, it generates custom CSS utility classes at build time. While sprinkles requires a pre-defined list of available values, Rainbow Sprinkles uses CSS custom properties to allow dynamic values using inline style variable assignments.

Compared to sprinkles:

- **Rainbow sprinkles ships a fraction of the CSS.** For each property, Sprinkles produces CSS that's a factor of `[pre-defined values] * [possible conditions]`. Rainbow sprinkles produces CSS that only scales with the number of conditions.
- **Supports dynamic values.** Rainbow Sprinkles uses dynamic inline style assignments to set the value of each property. You still get the TypeScript editor suggestions, but the ability to use any valid CSS value for that property.

<hr />

```tsx
function App() {
  return (
    // Use pre-defined values
    <Box bg="$blue50" margin="$large">
      {/* Or any valid CSS value */}
      <Box textAlign="center" fontSize="30px">
        Hello world!
      </Box>
    </Box>
  );
}
```

<hr />

[See a codesandbox demo here](https://codesandbox.io/s/crazy-cookies-i87ddj)

<hr />

## Setup

Install Rainbow Sprinkles.

```bash
npm install rainbow-sprinkles
```

Create a `rainbow-sprinkles.css.ts` file, then create and export your `rainbowSprinkles` function:

```typescript
// rainbow-sprinkles.css.ts
import { defineProperties, createRainbowSprinkles } from 'rainbow-sprinkles';

// or import a theme (e.g. `createTheme`, `createThemeContract`)
const vars = {
  space: {
    none: 0,
    small: '4px',
    medium: '8px',
    large: '16px',
    // etc.
  },
  colors: {
    blue50: '#eff6ff',
    blue100: '#dbeafe',
    blue200: '#bfdbfe',
    gray700: '#374151',
    gray800: '#1f2937',
    gray900: '#111827',
    // etc.
  },
};

const responsiveProperties = defineProperties({
  conditions: {
    mobile: {},
    tablet: { '@media': 'screen and (min-width: 768px)' },
    desktop: { '@media': 'screen and (min-width: 1024px)' },
  },
  defaultCondition: 'mobile',
  dynamicProperties: {
    // Define pre-determined values, which will be autosuggested
    color: vars.colors,
    backgroundColor: vars.colors,
    margin: vars.space,
    marginTop: vars.space,
    marginLeft: vars.space,
    marginRight: vars.space,
    marginBottom: vars.space,
    // Will work with any CSS value
    display: true,
    textAlign: true,
    flexDirection: true,
    justifyContent: true,
    alignItems: true,
  },
  staticProperties: {
    // Build out utility classes that don't use CSS variables
    display: ['block', 'flex', 'inline-block', 'inline-flex'],
  },
  shorthands: {
    bg: ['backgroundColor'],
    m: ['margin'],
    mr: ['marginRight'],
    ml: ['marginLeft'],
    mt: ['marginTop'],
    mb: ['marginBottom'],
    marginX: ['marginLeft', 'marginRight'],
    marginY: ['marginTop', 'marginBottom'],
    mx: ['marginLeft', 'marginRight'],
    my: ['marginTop', 'marginBottom'],
  },
});

export const rainbowSprinkles(responsiveProperties)

export type Sprinkles = Parameters<typeof rainbowSprinkles>[0];
```

Then set-up in your "host" component (in this case, a Box component):

```tsx
// Box.tsx
import { rainbowSprinkles, Sprinkles } from './rainbow-sprinkles.css';

interface BoxProps extends Sprinkles {
  children?: React.ReactNode;
}

export const Box = ({ children, ...props }: BoxProps) => {
  const { className, style, otherProps } = rainbowSprinkles(props);

  return (
    <div className={className} style={style} {...otherProps}>
      {children}
    </div>
  );
};
```

🎉 Good to go!

```tsx
// App.tsx
import { Box } from './Box';

function App() {
  return (
    // Use pre-defined values
    <Box bg="$blue50" margin="$medium $large">
      {/* Or any valid CSS value */}
      <Box textAlign="center" fontSize={{ mobile: '16px', desktop: '32px' }}>
        Hello world!
      </Box>
    </Box>
  );
}
```

### `dynamicProperties` vs `staticProperties`

One trade off that's made for supporting dynamic values is that we have to increase the size of the document. Instead of just appending a single class to an element to add a style, both a utility class and an inline style assignment is added to an element. While this setup will still produce an overall smaller bundle in many cases, some large applications may observe frequent recurrence of specific combinations of CSS properties and values. In these cases, those combinations can be set-up in `staticProperties` in the initial configuration. `staticProperties` will produce typical CSS utility classes. The runtime portion of Rainbow Sprinkles will defer to the CSS classes created by `staticProperties` and not apply any inline style assignments.

Here's an example scenario in which this property could be valuable. Your organization sets up Rainbow Sprinkles and sees widespread adoption. Your metrics reveal that the most frequently used prop/value combinations is `display="flex"` and `margin` with the application's theme variables. You can run an experiment to evaluate whether making these property/values combination static improves the bundle size.

```tsx
createRainbowSprinkles({
  dynamicProperties: {
    // Still support arbitrary values
    display: true,
    margin: true,
  },
  staticProperties: {
    // Also produce fixed CSS classes
    display: ['flex'],
    margin: vars.space,
  },
});
```

## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**. For detailed contributing guidelines, please see [CONTRIBUTING.md](CONTRIBUTING.md)

## Thanks

- [Vanilla Extract](https://vanilla-extract.style) for creating an inovative and configurable CSS preprocessor
- [Styled System](https://styled-system.com) for inventing theme-driven style props
- Homebase, Wayfair's design system, for providing interesting problems to solve

## License

Distributed under the MIT License. See `LICENSE` for more information.

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/roginfarrer"><img src="https://avatars.githubusercontent.com/u/9063669?v=4?s=100" width="100px;" alt="Rogin Farrer"/><br /><sub><b>Rogin Farrer</b></sub></a><br /><a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=roginfarrer" title="Code">💻</a> <a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=roginfarrer" title="Documentation">📖</a> <a href="#infra-roginfarrer" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a> <a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=roginfarrer" title="Tests">⚠️</a> <a href="https://github.com/wayfair/rainbow-sprinkles/pulls?q=is%3Apr+reviewed-by%3Aroginfarrer" title="Reviewed Pull Requests">👀</a> <a href="https://github.com/wayfair/rainbow-sprinkles/issues?q=author%3Aroginfarrer" title="Bug reports">🐛</a> <a href="#example-roginfarrer" title="Examples">💡</a> <a href="#ideas-roginfarrer" title="Ideas, Planning, & Feedback">🤔</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://matthamlin.me/"><img src="https://avatars.githubusercontent.com/u/5579638?v=4?s=100" width="100px;" alt="Matt Hamlin"/><br /><sub><b>Matt Hamlin</b></sub></a><br /><a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=hamlim" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/mooronic"><img src="https://avatars.githubusercontent.com/u/119345755?v=4?s=100" width="100px;" alt="mooronic"/><br /><sub><b>mooronic</b></sub></a><br /><a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=mooronic" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/skyesong"><img src="https://avatars.githubusercontent.com/u/3420442?v=4?s=100" width="100px;" alt="Cathy"/><br /><sub><b>Cathy</b></sub></a><br /><a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=skyesong" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/kbondanza"><img src="https://avatars.githubusercontent.com/u/8070092?v=4?s=100" width="100px;" alt="Kelly"/><br /><sub><b>Kelly</b></sub></a><br /><a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=kbondanza" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/simontaisne"><img src="https://avatars.githubusercontent.com/u/233639?v=4?s=100" width="100px;" alt="Simon Taisne"/><br /><sub><b>Simon Taisne</b></sub></a><br /><a href="https://github.com/wayfair/rainbow-sprinkles/commits?author=simontaisne" title="Code">💻</a> <a href="https://github.com/wayfair/rainbow-sprinkles/issues?q=author%3Asimontaisne" title="Bug reports">🐛</a> <a href="#ideas-simontaisne" title="Ideas, Planning, & Feedback">🤔</a></td>
    </tr>
  </tbody>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

## Contact

- The Homebase Engineering Team - [email](mailto:homebase-eng@wayfair.com)
- Rogin Farrer - [@roginfarrer](https://twitter.com/roginfarrer)
