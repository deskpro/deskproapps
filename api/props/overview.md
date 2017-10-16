Overview
========
The SDK automatically passes a number of objects to your components via props. Some of the SDK props contain information about the running application and current context, and some provide methods to interact with DeskPRO. For instance the [storage](#storage) prop which allows developers to persist values with the DeskPRO application, and the [me](#me) prop which contains information about the person using the app.

The following example uses the `tabData` prop to display a list of people participating in a ticket conversation. The prop is automatically passed to the component by the SDK, and automatically updates when a new ticket is opened.

```jsx
import React from 'react';

const App = ({ tabData }) => (
  <ul>
    {tabData.participants.map(({ person }) => (
      <li key={person.id}>
        <img src={person.default_picture_url} />
        <div>
          {person.name}
        </div>
        <div>
          {person.primary_email.email}
        </div>
      </li>
    ))}
  </ul>
);

export default App;
```