Me
==
`this.props.me`

An object which is populated with the details of the agent/admin using the app.

```js
import React from 'react';
import { sdkConnect } from '@deskpro/apps-sdk-react';

class Page extends React.Component {
    render() {
        const { me } = this.props;
        
        return (
            <div>
                Welcome, {me.name}!
            </div>
        );
    }
}

export default sdkConnect(Menu);
```

The object contains the following properties:

```js
{
    id:              number,
    avatar:          object,
    can_admin:       bool,
    can_agent:       bool,
    can_billing:     bool,
    is_agent:        bool,
    is_confirmed:    bool,
    is_contact:      bool,
    is_deleted:      bool,
    is_disabled:     bool,
    is_user:         bool,
    was_agent:       bool,
    online:          bool,
    labels:          array,
    teams:           array,
    phone_numbers:   array,
    date_created:    string,
    date_last_login: string,
    name:            string,
    display_name:    string,
    first_name:      string,
    last_name:       string,
    primary_email:   object,
    emails:          array,
    gravatar_url:    string,
    tickets_count:   number,
    timezone:        string
}
```
