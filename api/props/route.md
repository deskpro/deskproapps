Route
=====
`this.props.route`

A light-weight router which is used to display different "pages" within an app. Pages within apps do not have URLs like web pages, but they can be given simple labels like "settings" or "index", and `this.props.route` can then be used to switch between them.

The object contains two properties:

* `this.props.route.location` - A string with the name of the current page
* `this.props.route.params` - An object of key/value pairs associated with the location

The following example uses a `switch` statement to display a different page depending on the current location.

```js
import React from 'react';
import { sdkConnect } from '@deskpro/apps-sdk-react';
import PageAccount from './PageAccount';
import PageIndex from './PageIndex';

class Content extends React.Component {
    render() {
        const { route } = this.props;
        
        switch (route.location) {
          case 'account':
            return <PageAccount />;
          case 'index':
            return <PageIndex />;
        }
    }
}

export default sdkConnect(Content);
```

### Switching to a route

The location is changed by calling `this.props.route.to()` with the name of a page.

```js
this.props.route.to('index');
```

Params may also be passed along with the location.

```js
this.props.route.to('account', { id: 5 });
```

The following example renders two pages. An "index" page with a form where a note may be entered, and a "note" page which displays the note.

```jsx
// PageIndex.jsx
import React from 'react';
import { sdkConnect } from '@deskpro/apps-sdk-react';
import { Form, Input, Button } from '@deskpro/react-components/lib/bindings/redux-form';

class PageIndex extends React.Component {
    /**
     * Switches to the "note" page, passing the note value along with
     * the location.
     */
    handleSubmit = (values) => {
        this.props.route.to('note', { note: values.note });
    };
    
    /**
     * @returns {XML}
     */
    render() {
        return (
            <div>
                <Form onSubmit={this.handleSubmit}>
                    <Input
                        label="Note:"
                        id="note"
                        name="note"
                    />
                    <Button>Submit</Button>
                </Form>
            </div>
        );
    }
}

export default sdkConnect(PageIndex);
```

```jsx
// PageNote.jsx
import React from 'react';
import { sdkConnect } from '@deskpro/apps-sdk-react';

class PageNote extends React.Component {
    /**
     * Changes to the index page when the button is clicked.
     */
    handleClick = () => {
        this.props.route.to('index');
    };
    
    /**
     * @returns {XML}
     */
    render() {
        const { route } = this.props;
        
        return (
            <div>
                <p>{route.params.note}</p>
                <button onClick={this.handleClick}>
                    To index page
                </button>
            </div>
        );
    }
}

export default sdkConnect(PageNote);
```

### Events

Use the `this.props.route.on()` method to listen for route change events, and `this.props.route.off()` to stop listening. Currently the "to" event is supported, which is triggered prior to changing the route.

```js
import React from 'react';
import { sdkConnect } from '@deskpro/apps-sdk-react';
import PageAccount from './PageAccount';
import PageIndex from './PageIndex';

class Content extends React.Component {
    componentDidMount() {
        const { route } = this.props;
        
        route.on('to', this.handleRouteTo);
    }
    
    componentWillUnmount() {
        const { route } = this.props;
        
        route.off('to', this.handleRouteTo);
    }
    
    handleRouteTo = () => {
        const { route } = this.props;
        
        console.log(`Changing to location ${route.location}.`);
    };
    
    render() {
        const { route } = this.props;
        
        switch (route.location) {
          case 'account':
            return <PageAccount />;
          case 'index':
            return <PageIndex />;
        }
    }
}

export default sdkConnect(Content);
```


### Routes component

The [Routes](/components/routes.md) and [Route](/components/route.md) components may be used in place of a `switch` statement.

```jsx
// App.jsx
import React from 'react';
import { Routes, Route } from '@deskpro/apps-sdk-react';
import PageNote from './PageNote';
import PageIndex from './PageIndex';
import PageNotFound from './PageNotFound';

const App = () => (
  <Routes>
    <Route location="note" component={PageNote} />
    <Route location="index" component={PageIndex} />
    <Route defaultRoute component={PageNotFound} />
  </Routes>
);
```

The route with the matching location will be rendered. The route with `defaultRoute` will be rendered when no other route matches.

### Link component

Connected components may also change the location using the [Link](/components/link.md) and [LinkButton](/components/linkbutton.md) components.

```js
import React from 'react';
import { sdkConnect, Link, LinkButton } from '@deskpro/apps-sdk-react';

class Menu extends React.Component {
    render() {
        return (
            <nav>
                <ul>
                    <li>
                        <Link to="settings">
                            Settings
                        </Link>
                    </li>
                    <li>
                        <Link to="account" params={{ id: 5 }}>
                            Account
                        </Link>
                    </li>
                </ul>
                <LinkButton to="index">
                    Home
                </LinkButton>
            </nav>
        );
    }
}

export default sdkConnect(Menu);
```