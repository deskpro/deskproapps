Overview
========
This tutorial will walk you through creating a DeskPRO app which has two pages. One page with a settings form and one page which displays the form values. [App storage](/api/props/storage.md) will be used to save the values, and the [route object](/api/props/route.md) will be used to switch between the two pages.

![screenshot](/images/tutorials/form-1.png)

----

{% include "_prerequisites.md" %}

----

## Step 1. Clone the boilerplate
{% set projectDirectory = "form-tutorial" %}
{% include "_boilerplate_summary.md" %}

## Step 2. Update the manifest

{% include "_manifest_summary.md" %}

Edit the _package.json_, which can be found in the app root directory. Change the `deskpro.title` property to "Form Tutorial".

{% label %}package.json{% endlabel %}
```json
{
  "deskpro": {
    "version": "2.1.0",
    "title": "Form Tutorial",
    "isSingle": true,
    "scope": "agent",
    "targets": [
      {
        "target": "ticket-sidebar",
        "url": "html/index.html"
      }
    ],
    "storage": [
      {
        "name": "settings",
        "isBackendOnly": false,
        "permRead": "EVERYBODY",
        "permWrite": "EVERYBODY"
      }
    ]
  }
}
```

#### Explanation

The "title" value will be shown in the app toolbar.

#### See also

* [Manifest overview](/api/manifest.html)

## Step 3. Create the settings page
Create a new component with a form.

{% label %}src/main/javascript/PageForm.jsx{% endlabel %}
```jsx
import React from 'react';
import { sdkConnect } from '@deskpro/apps-sdk-react';
import { Container } from '@deskpro/react-components';
import { Form, Input, Button } from '@deskpro/react-components/lib/bindings/redux-form';

class PageForm extends React.Component {
  /**
   * Changes to the index page after the form is submitted and
   * the values have been written to app storage.
   *
   * @param {object} formValues
   */
  handleSubmit = (formValues) => {
    this.props.route.to('index');
  };

  /**
   * @returns {XML}
   */
  render() {
    const { storage } = this.props;
    const initialValues = (storage.app.settings || {});
    
    return (
      <Container>
        <Form
          name="settings"
          initialValues={initialValues}
          onSubmit={storage.onSubmitApp(this.handleSubmit)}
        >
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
      </Container>
    );
  }
}

export default sdkConnect(PageForm);
```

![screenshot](/images/tutorials/form-1.png)

#### Explanation

The form uses components from the [d@deskpro/react-components](https://github.com/deskpro/react-components) library, which is included in the boilerplate by default.

The [this.props.storage.onSubmitApp](/api/props/storage.md) function is passed to the form `onSubmit` handler to automatically save the submitted form values to app storage. The function saves the form values to the DeskPRO database using the form `name` prop ("settings") as the storage key. See the [storage documentation](/api/props/storage.md) for more information.

The `this.handleSubmit` function is then called by `this.props.storage.onSubmitApp` with the form values. The function switches to the index page using the SDK router. See the [route documentation](/api/props/route.md) for more information.

The form is initialized with the existing settings by passing `this.props.storage.app.settings` to the `initialValues` prop. This works because values written to app storage using the key "settings" can be read from storage via the prop `this.storage.app.settings`.

The `PageForm` component is connected to the SDK using the `sdkConnect` function. Which is required in order to read and write to storage. See the documentation on [connecting components to the SDK](/api/props/connecting.md) for more information.

> **[info]**
> The code uses `(storage.app.settings || {})` to assign a default value because the settings will not exist the first time you run the app.

<!-- -->
> **[info]**
>    The callback passed to `this.props.storage.onSubmitApp` is optional and may be omitted. Simply pass the function to the submit handler using `onSubmit={this.props.storage.onSubmitApp}` when post-processing of the form values is not required.

#### See also

* [Storage prop](/api/props/storage.html)


## Step 4. Create the index page
Create a new component to display the form values.

{% label %}src/main/javascript/PageIndex.jsx{% endlabel %}
```jsx
import React from 'react';
import { LinkButton, sdkConnect } from '@deskpro/apps-sdk-react';
import { Container } from '@deskpro/react-components';

class PageIndex extends React.Component {
  /**
   * @returns {XML}
   */
  render() {
    const { storage } = this.props;
    const settings = storage.app.settings;
    
    return (
      <Container>
        <div>
          <div>
            Client ID: {settings.clientId}
          </div>
          <div>
            Client Secret: {settings.clientSecret}
          </div>
        </div>
        <LinkButton to="form">
          Edit
        </LinkButton>
      </Container>
    );
  }
}

export default sdkConnect(PageIndex);
```

![screenshot](/images/tutorials/form-2.png)

#### Explanation

The settings were saved to app storage using the key "settings" which means they can be read from storage from `this.props.storage.app.settings`. See the [storage documentation](/api/props/storage.md) for more information.

The `LinkButton` component uses the SDK router to switch to the "form" page when clicked. See the [route documentation](/api/props/route.md) for more information.

The `PageIndex` component is connected to the SDK using the `sdkConnect` function. Which is required in order to read and write to storage. See the documentation on [connecting components to the SDK](/api/props/connecting.md) for more information.

#### See also

* [Storage prop](/api/props/storage.html)
* [Route prop](/api/props/route.md)


## Step 5. Modify the app component
Edit the app component to look like the following code.

{% label %}src/main/javascript/App.jsx{% endlabel %}
```jsx
import React from 'react';
import { Routes, Route } from '@deskpro/apps-sdk-react';
import { Container } from '@deskpro/react-components';
import PageForm from './PageForm';
import PageIndex from './PageIndex';

const App = () => (
  <Container>
    <Routes>
        <Route location="index" component={PageIndex} />
        <Route location="form" component={PageForm} />
    </Routes>
  </Container>
);

export default App;
```

#### Explanation

* The `Routes` components reads the `this.props.route.location` value to render the `Route` with the matching `location` prop. See the [Routes component documentation](/api/components/routes.md) for more information.

> **[info]**
> The `App` component is connected to the SDK automatically by the boilerplate, and does _not_ need to be wrapped with `sdkConnect`.

## Step 6. Run the dev server
{% include "_dev_server.md" %}

## Step 7. Deploy the app
{% include "_deploy_app.md" %}
