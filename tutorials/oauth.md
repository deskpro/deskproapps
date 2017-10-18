Overview
========
This tutorial will walk you through creating a DeskPRO app which uses oauth to authenticate with a remote service provider. This tutorial will use [GitHub](https://github.com/) as an example.

{% include "_oauth_overview.md" %}

----

{% include "_prerequisites.md" %}

----

## Step 1. Clone the boilerplate
{% set projectDirectory = "oauth-tutorial" %}
{% include "_boilerplate_summary.md" %}

## Step 2. Configure the manifest

{% include "_manifest_summary.md" %}

Edit the _package.json_, which can be found in the app root directory. Change the `deskpro.title` property to "OAuth Tutorial". Also add the "storage" values shown below.

{% label %}package.json{% endlabel %}
```json
"deskpro": {
  "version": "2.1.0",
  "title": "OAuth Tutorial",
  "isSingle": true,
  "scope": "agent",
  "storage": [
    {
      "name": "oauth:github",
      "isBackendOnly": true,
      "permRead": "EVERYBODY",
      "permWrite": "OWNER"
    },
    {
      "name": "settings",
      "isBackendOnly": false,
      "permRead": "EVERYBODY",
      "permWrite": "OWNER"
    },
    {
      "name": "user_settings",
      "isBackendOnly": false,
      "permRead": "OWNER",
      "permWrite": "OWNER"
    }
  ]
}
```

#### Explanation
@todo

## Step 3. Create a settings page
Create a new component with a form.

{% label %}src/main/javascript/PageSettings.jsx{% endlabel %}
```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';
import { Form, Input, Button } from 'deskpro-components/lib/bindings/redux-form';

class PageSettings extends React.PureComponent {
  /**
   * Called when the form is submitted
   */
  handleSubmit = (settings) => {
    const { oauth, route } = this.props;
    
    // Create a "connection" object using the submitted values, plus
    // the values for urlAccessToken and urlAuthorize. Then register
    // the connection with the oauth module and redirect to the
    // index page.
    const connection = Object.assign({}, settings, {
      urlAccessToken: 'https://github.com/login/oauth/access_token',
      urlAuthorize:   'https://github.com/login/oauth/authorize'
    });
    oauth.register('github', connection);
    route.to('index');
  };
  
  /**
   * @returns {XML}
   */
  render() {
    const { oauth, storage } = this.props;

    return (
      <Form
        name="settings"
        initialValues={storage.app.settings}
        onSubmit={storage.onSubmitApp(this.handleSubmit)}
        >
        <p>
          You must register this app with GitHub before using it.
          Fill out the app registration form and when you come to
          the "Authorization callback URL" field enter the following
          value:
        </p>
        <code>
          {oauth.providers.github.urlRedirect}
        </code>
        <Input
          label="Client ID"
          id="clientId"
          name="clientId"
        />
        <Input
          label="Client Secret"
          id="clientSecret"
          name="clientSecret"
        />
        <Button>
          Save
        </Button>
      </Form>
    );
  }
}

export default sdkConnect(PageSettings);
```

![screenshot](/images/tutorials/oauth-1.png)

#### Explanation
@todo

## Step 4. Create an index page
Create a new component to display the oauth access token.

{% label %}src/main/javascript/PageIndex.jsx{% endlabel %}
```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';

class PageIndex extends React.Component {
  /**
   * @returns {XML}
   */
  render() {
    const { storage } = this.props;
    
    return (
      <div>
        <p>
          {storage.app.user_settings ? (
            <p>
              Thanks for authenticating!
              Your access token is:
              {storage.app.user_settings.accessToken}
            </p>
          ) : (
            <p>
              Oop! Looks like authentication failed!
            </p>
          )}
        </p>
      </div>
    );
  }
}

export default sdkConnect(PageIndex);
```

![screenshot](/images/tutorials/oauth-2.png)
![screenshot](/images/tutorials/oauth-3.png)

#### Explanation
@todo

## Step 5. Modify the app component
Edit the app component to look like the following code.

{% label %}src/main/javascript/App.jsx{% endlabel %}
```jsx
import React from 'react';
import PageSettings from './PageSettings';
import PageIndex from './PageIndex';

export default class App extends React.Component {
  /**
   * Invoked immediately after a component is mounted
   */
  componentDidMount() {
    const { oauth, storage, route, ui } = this.props;

    // The app settings will be empty the first time the app is run.
    // Route to the settings page so the admin can setup oauth creds.
    if (!storage.app.settings) {
      return route.to('settings');
    }

    // Route to the index page if the user already has an access token.
    if (storage.app.user_settings.accessToken) {
      return route.to('index');
    }

    // Otherwise obtain the access token using oauth.
    oauth.access('github')
      // Save the access token and redirect to the index page.
      .then((resp) => {
        const user_settings = {
            accessToken: resp.accessToken
        };
        storage.setApp({ user_settings });
        return route.to('index');
      })
      // Send any errors directly to the UI to be displayed.
      .catch(ui.error);
  }
  
  /**
   * @returns {XML}
   */
  render() {
    return (
      <Routes>
        <Route location="settings" component={PageSettings} />
        <Route location="index" component={PageIndex} />
      </Routes>
    );
  }
}
```

![screenshot](/images/tutorials/oauth-4.png)

#### Explanation
@todo

## Step 6. Run the dev server
{% include "_dev_server.md" %}

## Step 7. Deploy the app
{% include "_deploy_app.md" %}
