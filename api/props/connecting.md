Connecting components
=====================
Components which need access to the SDK props must be _connected_ to the SDK. The [DeskproSDK component](/components/deskprosdk.md) automatically connects the wrapped component.

The `<App />` component in the following example will have all SDK props passed to it because it's wrapped by the `DeskproSDK` component.

```jsx
ReactDOM.render(
    <DeskproSDK dpapp={dpapp} store={store}>
      <App />
    </DeskproSDK>,
    document.getElementById('deskpro-app')
);
```

Only the app root component gets wrapped by `DeskproSDK`. The other components in your app will use the `sdkConnect` function to connect them to the SDK props. Its usage is optional, and is only required when a component needs access to the props.

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-react-sdk';

class PageSettings extends React.Component {
    render() {
        return (
            <div>
                {this.props.storage.app.country}
            </div>
        );
    }
}

export default sdkConnect(PageSettings);
```

The function may be used as a decorator when [decorators are enabled](https://babeljs.io/docs/plugins/transform-decorators/).

```jsx
import React from 'react';
import { sdkConnect } from 'deskpro-react-sdk';

@sdkConnect
export default class PageSettings extends React.Component {
    render() {
        return (
            <div>
                {this.props.storage.app.country}
            </div>
        );
    }
}
```

**Advanced usage**  
React SDK uses [Redux](http://redux.js.org/) internally to manage state. The same `mapStateToProps` and `mapDispatchToProps` functions [supported by React Redux](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) may also be passed to the `sdkConnect` function.

```js
import React from 'react';
import { sdkConnect } from 'deskpro-react-sdk';

class PageSettings extends React.Component {
    render() {
        return (
            <div>
                {this.props.country}
            </div>
        );
    }
}

const mapStateToProps(state) {
    return {
        country: state.sdk.storage.app.settings.country
    };
};

export default sdkConnect(PageSettings, mapStateToProps);
```