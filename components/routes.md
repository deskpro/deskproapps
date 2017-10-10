Overview
========
The SDK includes a light-weight router which displays different pages within DeskPRO apps. Route locations are defined using simple names instead of addresses, because DeskPRO apps do not use the browser location (URL). The current location is changed by calling [this.props.route.to()](/pages/props/#route) with the name of the new location, or by using the [Link](/pages/components/Link) and [LinkButton](/pages/components/LinkButton) components.

> **[info]**
> Components using the `Routes` component must be [connected to the SDK](/pages/props/#connecting-your-components) using `sdkConnect` or `DeskproSDK`.

## Props

```jsx
<Routes>
    <Route
        location={string}
        component={element}
        defaultRoute={bool}
    />
</Routes>
```

## Example

First create a few pages for the app.

{% label %}PageSettings.jsx{% endlabel %}
```jsx
import React from 'react';
import { Link, sdkConnect } from 'deskpro-sdk-react';

const PageSettings = () => (
    <div>
        <h1>Settings</h1>
        <Link to="index">
            Go to index
        </Link>
    </div>
);

export default sdkConnect(PageSettings);
```

{% label %}PageIndex.jsx{% endlabel %}
```jsx
import React from 'react';
import { Link, sdkConnect } from 'deskpro-sdk-react';

const PageSettings = () => (
    <div>
        <h1>Index</h1>
        <Link to="settings">
            Go to settings
        </Link>
    </div>
);

export default sdkConnect(PageSettings);
```

{% label %}PageNotFound.jsx{% endlabel %}
```jsx
import React from 'react';

const PageNotFound = () => (
    <div>
        <h1>Not Found</h1>
    </div>
);

export default PageNotFound;
```

> **[info]**
> The `PageNotFound` component doesn't use the SDK props or `Routes` component, and does not need to be connected using `sdkConnect` or `DeskproSDK`.

Now create the main app component which contains the `Routes`. The `Route` which matches the current location gets rendered. The route with the `defaultRoute` prop renders when no other routes matches the current location.

{% label %}App.jsx{% endlabel %}
```jsx
import React from 'react';
import { Routes, Route, sdkConnect } from 'deskpro-sdk-react';
import PageSettings from './PageSettings';
import PageIndex from './PageIndex';
import PageNotFound from './PageNotFound';

class App extends React.Component {
  render() {
    return (
      <Routes>
        <Route location="settings" component={PageSettings} />
        <Route location="index" component={PageIndex} />
        <Route defaultRoute component={PageNotFound} />
      </Routes>
    );
  }
}

export default sdkConnect(App);
```

Routes may also be defined using child components instead of the `component` prop.

```jsx
<Routes>
    <Route location="settings">
        <PageSettings />
    </Route>
    <Route location="index">
        <PageIndex />
    </Route>
    <Route defaultRoute>
        <h1>Not Found</h1>
    </Route>
</Routes>
```

## Changing routes manually

Call `this.props.route.to()` to manually change the route.

{% label %}PageSettings.jsx{% endlabel %}
```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';

class PageSettings extends Route.Component {
    handleClick = () => {
        this.props.route.to('index');
    };
    
    render() {
        return (
            <div>
                <h1>Settings</h1>
                <button onClick={this.handleClick}>
                    Go to index
                </button>
            </div>
        );
    }
};

export default sdkConnect(PageSettings);
```

The [Link](/components/link.md) and [LinkButton](/components/linkbutton.md) components may also be used to change routes.