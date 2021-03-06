# Cookbook

## How to add mixins or third-party plugins to the style guide?

If you need to have default data for use in the examples of your components, you can import mixins to the style guide. Creating a .js file that exports the mixins or adding it directly to the `styleguide.config.js` file

Use [mixins](Configuration.md#mixins) option to customize this behavior:

```javascript
// mixin file
export default {
  data() {
    return {
      colorDemo: 'blue',
      sizeDemo: 'large',
    }
  }
  /* ... */
}
```

````jsx
// example component

<Button size="colorDemo" color="sizeDemo">Lick Me</Button>
````

Also if you need to load a vue plugin from a third party, you can add it in the mixin file so that it is installed in the style guide

```javascript
// mixin file
import VeeValidate from 'vee-validate';

Vue.use(VeeValidate);

export default {
  /* ... */
}
```

## How to exclude some components from style guide?

Vue Styleguidist will ignore tests (`__tests__` folder and file names containing `.test.js` or `.spec.js`) by default.

Use [ignore](Configuration.md#ignore) option to customize this behavior:

```javascript
module.exports = {
  ignore: [
    'src/components/Button.vue'
  ]
};
```

## How to add custom JavaScript and CSS or polyfills?

In your style guide config:

```javascript
const path = require('path');
module.exports = {
  require: [
    'babel-polyfill',
    path.join(__dirname, 'path/to/script.js'),
    path.join(__dirname, 'path/to/styles.css'),
  ]
};
```

## How to change styles of a style guide?

Use config option [theme](Configuration.md#theme) to change fonts, colors, etc. and option [styles](Configuration.md#styles) to tweak style of particular Vue Styleguidist’s components:

```javascript
module.exports = {
  theme: {
    color: {
      link: 'firebrick',
      linkHover: 'salmon',
    },
    fontFamily: {
      base: '"Comic Sans MS", "Comic Sans", cursive'
    }
  },
  styles: {
    Logo: {
      logo: {
        animation: 'blink ease-in-out 300ms infinite'
      },
      '@keyframes blink': {
        to: { opacity: 0 }
      }
    }
  }
};
```

> **Note:** See available [theme variables](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/src/styles/theme.js).

> **Note:** Styles use [JSS](https://github.com/cssinjs/jss/blob/master/docs/json-api.md) with these plugins: [jss-isolate](https://github.com/cssinjs/jss-isolate), [jss-nested](https://github.com/cssinjs/jss-nested), [jss-camel-case](https://github.com/cssinjs/jss-camel-case), [jss-default-unit](https://github.com/cssinjs/jss-default-unit), [jss-compose](https://github.com/cssinjs/jss-compose).

> **Note:** Use [React Developer Tools](https://github.com/facebook/react-devtools) to find component and style names. For example a component `<LogoRenderer><h1 className="logo-524678444">…` corresponds to an example above.

> **Note:** See [example](https://github.com/vue-styleguidist/buefy-styleguide-example/blob/master/docs-styleguidist/styles.js).

## How to change the layout of a style guide?

You can replace any Styleguidist Vue component. But in most of the cases you’ll want to replace `*Renderer` components — all HTML is rendered by these components. For example `ReactComponentRenderer`, `ComponentsListRenderer`, `PropsRenderer`, etc. — [check the source](https://github.com/vue-styleguidist/vue-styleguidist/tree/master/src/rsg-components) to see what components are available.

There’s also a special wrapper component — `Wrapper` — that wraps every example component. By default it just renders `children` as is but you can use it to provide a custom logic.

For example you can replace the `Wrapper` component to wrap any example in the [React Intl’s](https://github.com/yahoo/react-intl) provider component. You can’t wrap the whole style guide because every example is compiled separately in a browser.

```javascript
// styleguide.config.js
const path = require('path');
module.exports = {
  styleguideComponents: {
    Wrapper: path.join(__dirname, 'lib/styleguide/Wrapper')
  }
};
```

```jsx
// lib/styleguide/Wrapper.js
import React, { Component } from 'react';
import { IntlProvider } from 'react-intl';
export default class Wrapper extends Component {
  render() {
    return (
      <IntlProvider locale="en">
        {this.props.children}
      </IntlProvider>
    );
  }
}
```

You can replace the `StyleGuideRenderer` component like this:

```javascript
// styleguide.config.js
const path = require('path');
module.exports = {
  styleguideComponents: {
    StyleGuideRenderer: path.join(__dirname, 'lib/styleguide/StyleGuideRenderer')
  }
};
```

```jsx
// lib/styleguide/StyleGuideRenderer.js
import React from 'react';
const StyleGuideRenderer = ({ title, homepageUrl, components, toc, hasSidebar }) => (
  <div className="root">
    <h1>{title}</h1>
    <main className="wrapper">
      <div className="content">
        {components}
        <footer className="footer">
          <Markdown text={`Generated with [React Styleguidist](${homepageUrl})`} />
        </footer>
      </div>
      {hasSidebar &&
        <div className="sidebar">
          {toc}
        </div>
      }
    </main>
  </div>
);
```

## How to change style guide dev server logs output?

You can modify webpack dev server logs format changing `stats` option of webpack config:

```javascript
module.exports = {
  webpackConfig(env) {
    if (env === 'development') {
      return {
        stats: {
          chunks: false,
          chunkModules: false,
          chunkOrigins: false,
        },
      };
    }
    return {};
  }
};
```

## How to debug my components and examples?

1. Open your browser’s developer tools
2. Write `debugger;` statement wherever you want: in a component source, a Markdown example or even in an editor in a browser.

![](http://rafaelescala.com/vue-styleguide/debug.png)

## How to debug the exceptions thrown from my components?

1. Put `debugger;` statement at the beginning of your code.
2. Press the ![Debugger](https://d3vv6lp55qjaqc.cloudfront.net/items/2h2q3N123N3G3R252o41/debugger.png) button in your browser’s developer tools.
3. Press the ![Continue](https://d3vv6lp55qjaqc.cloudfront.net/items/3b3c1P3g3O1h3q111I2l/continue.png) button and the debugger will stop execution at the next exception.

## How to use Vagrant with Styleguidist?

First read [Vagrant guide](https://webpack.js.org/guides/development-vagrant/) from the webpack documentation. Then enable polling in your webpack config:

```js
devServer: {
  watchOptions: {
    poll: true
  }
}
```

## How to reuse project’s webpack config?

See in [configuring webpack](Webpack.md#reusing-your-projects-webpack-config).
