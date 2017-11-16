Testing
=======
In this section you will learn how to use `testDpapp` and `testStore` to create snapshot tests for your app components.

<!-- toc -->

## Example
The following example assumes the app main component has been saved at `src/main/javascript/App.jsx`. After running the test a new file will be created at `src/test/unit/__snapshots__/App.test.jsx.snap`, which is the result of the snapshot test.

{% label %}src/test/unit/App.test.jsx{% endlabel %}
```jsx
import React from 'react';
import renderer from 'react-test-renderer';
import { DeskproSDK, testDpapp, testStore } from '@deskpro/apps-sdk-react';
import App from '../../main/javascript/App';

test('successfully render the application initial state', () => {
  const component = renderer.create(
    <DeskproSDK dpapp={testDpapp} store={testStore} ready={true}>
      <App />
    </DeskproSDK>
  );
  const tree = component.toJSON();
  expect(tree).toMatchSnapshot();
});
```

Run the test with the following command from the project root directory.

```
npm run test
```

## Explanation

```js
import { DeskproSDK, testDpapp, testStore } from '@deskpro/apps-sdk-react';
```

The `testDpapp` and `testStore` objects are mocks for the real dpapp and store.

```js
const component = renderer.create();
```

The `create()` method from the `react-test-renderer` package renders a DOM presentation of the component. See the [react-test-render documentation](https://www.npmjs.com/package/react-test-renderer) for more information.

```jsx
<DeskproSDK dpapp={testDpapp} store={testStore} ready={true}>
  <App />
</DeskproSDK>
```

The `<App />` component must be wrapped by the `<DeskproSDK>` component, but for testing the mock dpapp and store get passed to `<DeskproSDK>`. The initial `ready` value is also set to true to prevent `<DeskproSDK>` from rendering the loading animation.