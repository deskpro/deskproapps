Welcome!
========

DeskPRO React SDK is a library for connecting [React](https://reactjs.org/) components to the DeskPRO API.

## A Simple App

Creating rich DeskPRO apps is quick and easy using the React SDK.

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

![screenshot](/images/tutorials/basic-1.png)

## Tutorials

#### [Basic](tutorials/basic.html)
A tutorial that walks you through creating a simple DeskPRO app which displays a list of people participating in a ticket.

#### [Forms](tutorials/form.html)
A tutorial that walks you through creating a DeskPRO app which has two pages. One page with a settings form and one page which displays the form values.

#### [OAuth](tutorials/oauth.html)
A tutorial that walks you through creating a DeskPRO app which uses oauth to authenticate with a remote service provider.

#### [Redux](tutorials/redux.html)
A tutorial that walks advanced developers through using Redux with the SDK.
