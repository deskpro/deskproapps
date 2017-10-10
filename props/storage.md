Storage
=======
`this.props.storage`

An object which reads and writes values to the DeskPRO database in order to persist them from one invocation of the app and another. Two mechanisms are provided for storing values. One for storing global "app" values, and one for attaching "entity" values to the currently opened ticket.

> **[info]**
> See the [manifest storage configuration](/manifest.md) for more information about declaring storage values and setting permissions.

### setApp

Call `this.props.storage.setApp()` to persist global values which will be bound the app. For instance app settings or user information. The method takes an object of key/value pairs.

```js
this.props.storage.setApp({ country: 'uk' });
```

Once the values have been saved the SDK will pass new props to the connected components with the new values. The app values can then be read from the `this.props.storage.app` object.

```js
const country = this.props.storage.app.country;
```

The following example renders a form with a list of countries. The component uses `this.props.storage` to save the selected value.

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';
import { Form, Select, Button } from 'deskpro-components/lib/bindings/redux-form';

class PageCountry extends React.Component {
    /**
     * Receives the submitted form values and saves them to
     * app storage
     */
    handleSubmit = (values) => {
        this.props.storage.setApp({ country: values.country });
    };
    
    /**
     * @returns {XML}
     */
    render() {
        const initialValues = {
            country: this.props.storage.app.country
        };
        const options = [
            { label: 'United Kingdom', value: 'uk' },
            { label: 'United States', value: 'us' }
        ];

        return (
            <Form onSubmit={this.handleSubmit} initialValues={initialValues}>
                <Select
                    label="Country"
                    id="country"
                    name="country"
                    options={options}
                />
                <Button>Submit</Button>
            </Form>
        );
    }
}

export default sdkConnect(PageCountry);
```

### setEntity

Call `this.props.storage.setEntity()` to attach values to the currently opened ticket. Unlike the global app values, entity values are unique to each ticket. The method takes an object of key/value pairs.

```js
this.props.storage.setEntity({ note: '...' });
```

> **[info]**
> Under the hood the SDK saves the value using key "note:{ticket_id}". Which means the value of "note" is unique to the ticket being viewed.

Once the values have been saved they can be read from the `this.props.storage.entity` object.

```js
const note = this.props.storage.entity.note;
```

The following example allows agents to attach notes to the opened ticket. It shows the current notes with a form to create a new note.

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';
import { Form, Textarea, Button } from 'deskpro-components/lib/bindings/redux-form';

class Notes extends React.Component {
    /**
     * Receives the submitted form values and saves them to
     * app storage
     */
    handleSubmit = (values) => {
        const { storage } = this.props;
        const notes = storage.entity.notes;
        
        // Clone the existing notes or create a new array if the ticket
        // doesn't have any existing notes.
        const newNotes = (notes || []).slice(0);
        newNotes.push(values.note);
        
        // Attach the notes to the open ticket. This will overwrite the
        // existing value.
        storage.setEntity({ notes: newNotes });
    };
    
    /**
     * @returns {XML}
     */
    render() {
        const { storage } = this.props;
        const notes = storage.entity.notes;

        return (
            <div>
                <h1>Notes</h1>
                <ul>
                    {notes.map((note) => (
                        <li>{note}</li>
                    ))}
                </ul>
                
                <Form onSubmit={this.handleSubmit}>
                    <Textarea
                        label="Add note"
                        id="note"
                        name="note"
                    />
                    <Button>Submit</Button>
                </Form>
            
            </div>
        );
    }
}

export default sdkConnect(Notes);
```

### onSubmit

The SDK can automatically save form values to app/entity storage with `this.props.storage.onSubmitApp` and `this.props.storage.onSubmitEntity`. Use them as the form `onSubmit` handler, and the form values will be written to storage using the name of the form as the storage key.

The following example uses `this.props.storage.onSubmitApp` to automatically save the form values to app storage. The values will be saved using the key "settings".

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';
import { Form, Input, Button } from 'deskpro-components/lib/bindings/redux-form';

class PageSettings extends React.Component {
  render() {
    const { storage } = this.props;
    const settings = storage.app.settings || {};
    
    /**
     * @returns {XML}
     */
    return (
        <div>
            <div>Client ID: {settings.clientId}</div>
            <Form name="settings" onSubmit={storage.onSubmitApp}>
              <Input
                id="clientId"
                name="clientId"
              />
            </Form>
            <Button>Submit</Button>
        </div>
    );
}
```

An optional callback may be passed to the submit handlers which is called after the form values have been successfully written to storage.

```jsx
class PageSettings extends React.Component {
    /**
     * Called after the values have been saved to storage
     */
    handleSubmit = (values) => {
        console.log(values);
        this.props.route.to('index');
    };
    
    /**
     * @returns {XML}
     */
    render() {
        const { storage } = this.props;
        
        return (
            <Form
              name="settings"
              onSubmit={storage.onSubmitApp(this.handleSubmit)}
            >
              <Input
                id="clientId"
                name="clientId"
              />
            </Form>
        );
    }
}
```
