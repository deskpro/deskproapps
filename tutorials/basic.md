Overview
========
This tutorial will walk you through creating a simple DeskPRO app which displays a list of people participating in a ticket.

![screenshot](/images/tutorials/basic-1.png)

----

{% include "_prerequisites.md" %}

----

## Step 1. Clone the boilerplate
{% set projectDirectory = "basic-tutorial" %}
{% include "_boilerplate_summary.md" %}

## Step 2. Update the manifest

{% include "_manifest_summary.md" %}

Edit the _package.json_, which can be found in the app root directory. Change the `deskpro.title` property to "Participants".

{% label %}package.json{% endlabel %}
```json
{
  "deskpro": {
    "version": "2.1.0",
    "title": "Participants",
    "isSingle": true,
    "scope": "agent",
    "targets": [
      {
        "target": "ticket-sidebar",
        "url": "html/index.html"
      }
    ]
  }
}
```

#### Explanation

The "title" value is shown in the app toolbar.

#### See also

* [Manifest overview](/api/manifest.html)

## Step 3. Modify the app component
Edit the app component to look like the following code.

{% label %}src/main/javascript/App.jsx{% endlabel %}
```jsx
import React from 'react';
import { Container, Avatar } from '@deskpro/react-components';

export default class App extends React.Component {
  render() {
    const { tabData } = this.props;
    
    return (
      <Container>
        <ul className="participants-list">
          {tabData.participants.map((p) => (
            <li key={p.person.id}>
              <Avatar src={p.person.default_picture_url} />
              <div>
                {p.person.name}
              </div>
              <div>
                {p.person.primary_email.email}
              </div>
            </li>
          ))}
        </ul>
      </Container>
    );
  }
}
```

#### Explanation

The SDK automatically injects the [tabData](/api/props/tabdata.md) prop into the component. This prop is a plain object which contains information about the open ticket. The code above iterates over the `tabData.participants` array and displays an avatar for each person.

#### See also

* [TabData prop](/api/props/tabdata.html)

## Step 4. Modify the app styles
Modify the stylesheet to look like the following.

{% label %}src/main/sass/index.scss{% endlabel %}
```sass
$dp-styles-font-path: "~@deskpro/react-components/src/styles/fonts/";
@import "~@deskpro/react-components/src/styles/main.scss";

.participants-list {
  list-style-type: none;
  
  li {
    margin-bottom: 1rem;
  }
}
```

## Step 5. Run the dev server
{% include "_dev_server.md" %}

## Step 6. Deploy the app
{% include "_deploy_app.md" %}
