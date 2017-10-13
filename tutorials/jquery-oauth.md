Overview
========
This tutorial will walk you through creating a DeskPRO app which uses oauth to authenticate with a remote service provider. This tutorial will use [GitHub](https://github.com/) as an example.

## OAuth review
Before starting the tutorial let's go over designing DeskPRO apps to use the oauth work flow.

#### 1. Settings
DeskPRO admins must configure the app for oauth on the first run. Configuration is handled by showing the admin a form which has a "Authorization callback URL" value and inputs to enter a client ID and client secret.
![screenshot](/images/tutorials/oauth-1.png)

#### 2. Registration
The admin is instructed to visit their [GitHub account settings](https://github.com/settings/developers) in order to register the app. The admin will provide the "Authorization callback URL" to the registration form.
![screenshot](/images/tutorials/oauth-5.png)

#### 3. Credentials
On the next page the admin will be given a client ID and client secret.
![screenshot](/images/tutorials/oauth-6.png)

#### 4. Saving
The admin comes back to the app settings form where they enter and save the client ID and client secret. This concludes configuring the app for oauth, and the admin may close the window.
![screenshot](/images/tutorials/oauth-7.png)

#### 5. Authorization
Now when an agent opens the app they will be prompted to authenticate with GitHub.
![screenshot](/images/tutorials/oauth-4.png)

#### 6. Success
Upon successful authentication the agent will be shown the index page.
![screenshot](/images/tutorials/oauth-2.png)

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
      "name": "oauth:github",
      "isBackendOnly": true,
      "permRead": "EVERYBODY",
      "permWrite": "OWNER"
    },
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
    }
  ]
}
```

#### Explanation
@todo

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

The mount point for the app. Initially set to be invisible to hide the contents until the app is ready to run. The children of the element includes two "pages." An index page which displays the user access token, and a settings page with a form which is used to change the settings.


## Step 4. Run the dev server
Make sure DeskPRO is running on your computer, and then from the app root directory run the following command.

```
npm run dev
```

The `dev` script builds your app and starts a development server which communicates with DeskPRO to install the app. Open [https://localhost/agent/?appstore.environment=development](http://localhost/agent/?appstore.environment=development) when the the `dev` command finishes building to view the finished app.