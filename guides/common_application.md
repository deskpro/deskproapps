Common application use cases
============================
In this section, you will see code examples for the most common operations.

<!-- toc -->

## Saving and reading storage

Saving application-scoped storage:

```js
const value = {
  aString: 'a string',
  aNumber: 5,
  aBoolean : false
};

const { dpapp } = this.props;    
dpapp.storage.setAppStorage('app-settings', value).then(savedValue => console.log(savedValue));
```

Reading application-scoped storage:

```js
const { dpapp } = this.props;    
dpapp.storage.getAppStorage('app-settings').then(value => console.log(value));
```

Saving context-scoped storage: 

```js
const cardId = 5;

const { dpapp } = this.props;    
dpapp.storage.setEntityStorage('linked-card', cardId).then(savedValue => console.log(savedValue));
```

Reading application-scoped storage:

```js
const { dpapp } = this.props;    
dpapp.storage.getEntityStorage('app-settings').then(value => console.log(value));
```    
    

## Make REST calls

Making rest calls to DeskPRO APIs (the apps use the v2 api endpoints):

```js
const { dpapp } = this.props;    
dpapp.restApi.fetch(
  'me', 
  { 
    method: "PUT",
    headers: { 'Accept': 'application/json','Content-Type': 'application/json' }
  }
).then(response => console.log(response.body));
```

Making rest calls to an API which does not have CORS enabled:

```js
const { dpapp } = this.props;    
dpapp.restApi.fetchCORS(
  'https://login.mailchimp.com/oauth2/metadata', 
  { 
    method: "GET",
    headers: { 'Accept': 'application/json','Content-Type': 'application/json' }
  }
).then(response => console.log(response.body));
```

## Interact with the main DeskPRO User interface

Show a notification message inside DeskPRO:

```js
const { dpapp } = this.props;    
dpapp.deskproWindow.showNotification('Your notification message here');
```

## Interact with your application's UI Container

Show or hide your application:

```js
const { dpapp } = this.props;    
dpapp.ui.hide();
```

```js
const { dpapp } = this.props;    
dpapp.ui.show();
```

Change your application layout to `collapsed` (only the menu bar is visible) and then restore it to `expanded`

```js
const { dpapp } = this.props;    
dpapp.ui.collapse();
```

```js
const { dpapp } = this.props;    
dpapp.ui.expand();
```

Display a badge count next your application icon:

```js
const { dpapp } = this.props;    
dpapp.ui.badgeCount = 5;
dpapp.ui.showBadgeCount();
```

Display a loading screen while your application is loading some assets  or hide it:

```js
const { dpapp } = this.props;
// show the loading screen
dpapp.ui.showLoading();

// hide the loading screen
dpap.ui.hideLoading();
```

Show or hide the menu options:

```js
const { dpapp } = this.props;    
dpapp.deskproWindow.showNotification('Your notification message here');
```