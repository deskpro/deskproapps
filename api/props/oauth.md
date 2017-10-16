OAuth
=====
`this.props.oauth`

An object which authenticates with remote services and stores oauth credentials.

Save oauth connection settings.

```js
const connection = {
  urlAccessToken: OAUTH_ACCESS_URI,
  urlAuthorize:   OAUTH_AUTH_URI,
  clientId:       '...',
  clientSecret:   '...'
};

this.props.oauth.register('provider_name', connection);
```

Authenticate with the oauth provider and save the access token.

```js
this.props.oauth.access('provider_name')
  .then(({ accessToken }) => {
    this.props.storage.setApp({ user_settings: { accessToken } });
  });
```

Read the app oauth provider settings.

```js
const provider_settings = this.props.oauth.providers.provider_name;
```