UI
==
`this.props.ui`

An object containing methods which allow you to manipulate the UI.

### setLoading

Call `this.props.ui.setLoading(true)` to display the loading animation, and `this.props.ui.setLoading(false)` to turn it off.

![screenshot](/images/props-ui-2.png)

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-react-sdk';

class Hello extends React.Component {
    componentWillUpdate() {
        this.props.ui.setLoading(true);
    }
    
    componentDidUpdate() {
        this.props.ui.setLoading(false);
    }
    
    render() {
        return (
            <div>Hello!</div>
        );
    }
}

export default sdkConnect(Hello);
```

### setBadgeCount

Call `this.props.ui.setBadgeCount()` to set the badge number displayed above the app icon. The badge is invisible when the value is zero.

![screenshot](/images/props-ui-1.png)

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-react-sdk';

class Hello extends React.Component {
    componentDidMount() {
        this.props.ui.setBadgeCount(3);
    }
    
    render() {
        return (
            <div>Hello!</div>
        );
    }
}

export default sdkConnect(Hello);
```
