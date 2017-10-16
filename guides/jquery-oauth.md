Overview
========
This tutorial will walk you through creating a DeskPRO app which uses oauth to authenticate with a remote service provider. This tutorial will use [GitHub](https://github.com/) as an example.

!INCLUDE "_oauth_overview.md"

----

To follow this tutorial you will need:

* A [GitHub](https://github.com/) account
* [DeskPRO running on your computer](https://github.com/deskpro/deskpro/blob/develop/README.md)
* NPM 6 or greater
* Git 1.9 or greater
* A IDE or text editor

----

## Step 1. Clone the boilerplate
The SDK boilerplate includes the basic app configuration and files to help developers get started writing apps.

```
git clone https://github.com/deskpro/deskproapps-boilerplate-react oauth-tutorial
cd oauth-tutorial
npm install
```

## Step 2. Configure the manifest
Edit the app configuration in _package.json_, which can be found in the app root directory. Change the "title" property to "OAuth Tutorial". Also add the "storage" values shown below.

{% label %}package.json{% endlabel %}
```json
"deskpro": {
  "version": "2.1.0",
  "title": "OAuth Tutorial",
  "isSingle": true,
  "scope": "agent",
  "storage": [
    {
      "name": "settings",
      "isBackendOnly": false,
      "permRead": "EVERYBODY",
      "permWrite": "OWNER"
    },
    {
      "name": "user_settings",
      "isBackendOnly": false,
      "permRead": "OWNER",
      "permWrite": "OWNER"
    },
    {
      "name": "oauth:github",
      "isBackendOnly": true,
      "permRead": "EVERYBODY",
      "permWrite": "OWNER"
    }
  ]
}
```

#### Explanation

```
"title": "OAuth Tutorial"
```

The "title" value will be shown in the app toolbar.

```
{
  "name": "settings",
  "isBackendOnly": false,
  "permRead": "EVERYBODY",
  "permWrite": "OWNER"
},
{
  "name": "user_settings",
  "isBackendOnly": false,
  "permRead": "OWNER",
  "permWrite": "OWNER"
},
{
  "name": "oauth:github",
  "isBackendOnly": true,
  "permRead": "EVERYBODY",
  "permWrite": "OWNER"
}
```

Declares permissions on values which will be written to and read from app storage.


## Step 3. Modify the HTML
Edit the app HTML file to look like the following.

{% label %}src/main/html/index.html{% endlabel %}
```html
<!DOCTYPE html>
<html lang="en" class="deskpro-sidebar">
  <head>
    <style type="text/css" media="all">
      @import url("../assets/main.css");
    </style>
  </head>
  <body>
    <div id="deskpro-app" class="deskpro-app">
    
      <!-- index page -->
      <div id="index-page">
        <p>
          Thanks for authenticating!<br />
          Your access token is: <span id="span-accessToken"></span>
        </p>
      </div>

      <!-- settings page -->
      <div id="settings-page">
        <form name="settings" id="settings-form">
          <div class="dp-form-group">
            <label for="clientId" class="dp-input__label">
              Client ID
            </label>
            <div class="dp-input">
              <input id="clientId" name="clientId" />
            </div>
          </div>
          <div class="dp-form-group">
            <label for="clientSecret" class="dp-input__label">
              Client Secret
            </label>
            <div class="dp-input">
              <input id="clientSecret" name="clientSecret" />
            </div>
          </div>
          <button class="dp-button dp-button--l">
            Save
          </button>
        </form>
      </div>
    </div>

    <script src="https://code.jquery.com/jquery-3.2.1.js"></script>
    <script src="../assets/vendor.js"></script>
    <script src="../assets/main.js"></script>
    
    <script>
      function deskproapp(dpapp) {
        var $appContainer     = $('#deskpro-app');
        var $indexPage        = $('#index-page');
        var $settingsPage     = $('#settings-page').hide(),
            $form             = $('#settings-form'),
            $spanAccessToken  = $('#span-accessToken');
        
        var settings     = {},
            userSettings = {};
            
        // Callback for the form 'submit' event.
        var handleSubmit = function(e) {
          e.preventDefault();

          settings = {};
          var submitted = $form.serializeArray();
          for (var i = 0; i < submitted.length; i++) {
            var value = submitted[i];
            settings[value.name] = value.value;
          }

          return dpapp.storage.setAppStorage('settings', settings)
            .then(function() {
              $indexPage.show();
              $settingsPage.hide();
              return true;
            });
        };

        // Callback for getAppStorage('settings')
        var handleAppSettings = function(resp) {
          settings = resp;
          if (settings) {
            $form.find('[name="clientId"]').val(settings.clientId);
            $form.find('[name="clientSecret"]').val(settings.clientSecret);

            return dpapp.storage.getAppStorage('user_settings')
                .then(handleUserSettings);
          } else {
            $indexPage.hide();
            $settingsPage.show();
          }
        };
        
        // Callback for getAppStorage('user_settings')
        var handleUserSettings = function(resp) {
          userSettings = resp;
          if (userSettings) {
            $spanAccessToken.text(userSettings.accessToken);
            $indexPage.show();
            $settingsPage.hide();
            
            return Promise.resolve({});
          } else {
            return dpapp.oauth.access('github')
              .then(handleOAuthAccess);
          }
        };
        
        // Callback for oauth.access()
        var handleOAuthAccess = function(resp) {
          userSettings = {
            accessToken: resp.accessToken
          };
          
          return dpapp.storage.setAppStorage('user_settings', userSettings)
            .then(function() {
              $indexPage.show();
              $settingsPage.hide();
            });
        };

        $form.on('submit', handleSubmit);
        dpapp.storage.getAppStorage('settings')
          .then(handleAppSettings);
        $appContainer.show();
      }
    </script>
  </body>
</html>
```

#### Explanation

```html
<div id="deskpro-app" class="deskpro-app" style="display: none;">
```

The mount point for the app. Initially set to be invisible to hide the contents until the app is ready to run.

```html
<script src="https://code.jquery.com/jquery-3.2.1.js"></script>
<script src="../assets/vendor.js"></script>
<script src="../assets/main.js"></script>
```

Includes jQuery and scripts needed by the SDK.

```js
function deskproapp(dpapp) {

}
```

The SDK calls the function `deskproapp` to start running the app code. It passes an instance of `dpapp` to the function, which is used to interact with the DeskPRO API.

```js
var settings     = {},
    userSettings = {};
```

Variables which will hold the values declared in the manifest "storage" configuration.

```js
var handleSubmit = function(e) {
  e.preventDefault();

  settings = {};
  var submitted = $form.serializeArray();
  for (var i = 0; i < submitted.length; i++) {
    var value = submitted[i];
    settings[value.name] = value.value;
  }

  return dpapp.storage.setAppStorage('settings', settings)
    .then(function() {
      $indexPage.show();
      $settingsPage.hide();
      return true;
    });
};
```

Function which gets bound to the settings form "submit" event. It converts the form values into an object of key/value pairs, and then calls `dpapp.storage.setAppStorage('settings', settings)` to save those values to app storage. Once saved, the app hides the settings form and shows the index page.

```js
var handleAppSettings = function(resp) {
  settings = resp;
  if (settings) {
    $form.find('[name="clientId"]').val(settings.clientId);
    $form.find('[name="clientSecret"]').val(settings.clientSecret);

    return dpapp.storage.getAppStorage('user_settings')
        .then(handleUserSettings);
  } else {
    $indexPage.hide();
    $settingsPage.show();
  }
};
```

The app settings are fetched from app storage by calling `dpapp.storage.getAppStorage('settings')`, which returns a promise. The `handleAppSettings()` function gets called by the promise with the storage values.

```js
var handleUserSettings = function(resp) {
  userSettings = resp;
  if (userSettings) {
    $spanAccessToken.text(userSettings.accessToken);
    $indexPage.show();
    $settingsPage.hide();

    return Promise.resolve({});
  } else {
    return dpapp.oauth.access('github')
      .then(handleOAuthAccess);
  }
};
```

The user settings are fetched from app storage by calling `dpapp.storage.getAppStorage('user_settings')`, which returns a promise. The `handleUserSettings()` function gets called by the promise with the values.

```js
var handleOAuthAccess = function(resp) {
  userSettings = {
    accessToken: resp.accessToken
  };

  return dpapp.storage.setAppStorage('user_settings', userSettings)
    .then(function() {
      $indexPage.show();
      $settingsPage.hide();
    });
};
```

Called by the promise returned by the `dpapp.oauth.access('github')` function.

```js
$form.on('submit', handleSubmit);
```

Binds the submit handler to the form.

```js
dpapp.storage.getAppStorage('settings')
  .then(handleAppSettings);
```

Fetches the value for "settings" from app storage. Passes the handler to the promise returned by `dpapp.storage.getAppStorage('settings')`.

```js
$appContainer.show();
```

Finally the app is displayed.


## Step 4. Run the dev server
!INCLUDE "_dev_server.md"

## Step 5. Deploy the app
!INCLUDE "_deploy_app.md"
