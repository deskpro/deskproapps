Overview
========
This tutorial will walk you through creating a DeskPRO app which has two pages. One page with a settings form and one page which displays the form values. [App storage](/props/storage.md) will be used to save the values, and the [route object](/props/route.md) will be used to switch between the two pages.

![screenshot](/images/guides/form-1.png)

----

!INCLUDE "_prerequisites.md"

----

## Step 1. Clone the boilerplate
The SDK boilerplate includes the basic app configuration and files to help developers get started writing apps.

```
git clone https://github.com/deskpro/deskproapps-boilerplate-react form-tutorial
cd form-tutorial
npm install
```

## Step 2. Update the manifest
Edit the app configuration in _package.json_, which can be found in the app root directory. Change the "title" property to "Form Tutorial".

{% label %}package.json{% endlabel %}
```json
{
  "deskpro": {
    "version": "2.1.0",
    "title": "Form Tutorial",
    "isSingle": true,
    "scope": "agent",
    "targets": [
      {
        "target": "ticket-sidebar",
        "url": "html/index.html"
      }
    ],
    "storage": [
      {
        "name": "settings",
        "isBackendOnly": false,
        "permRead": "EVERYBODY",
        "permWrite": "EVERYBODY"
      }
    ]
  }
}
```

#### Explanation 

```
"title": "Form Tutorial"
```

The "title" value will be shown in the app toolbar. See the [manifest documentation](/manifest.md) for more information.

```
"storage": [
  {
    "name": "settings",
    "isBackendOnly": false,
    "permRead": "EVERYBODY",
    "permWrite": "EVERYBODY"
  }
]
```

The app will be writing to and reading from DeskPRO app storage, which is a type of key/value store. The "storage" manifest property declares the storage names and permissions. See the [manifest documentation](/manifest.md) for more information.

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
    <div id="deskpro-app" class="deskpro-app" style="display: none;">
      <!-- index page -->
      <div id="index-page">
        <div>
          <div>
            Client ID:
            <span id="span-clientId"></span>
          </div>
          <div>
            Client Secret:
            <span id="span-clientSecret"></span>
          </div>
          <button id="settings-btn" class="dp-button dp-button--l">
            Settings
          </button>
        </div>
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
        var $settingsPage     = $('#settings-page').hide();
        var $form             = $('#settings-form');
        var $spanClientId     = $('#span-clientId');
        var $spanClientSecret = $('#span-clientSecret');
        
        $('#settings-btn').on('click', function() {
          $indexPage.hide();
          $settingsPage.show();
        });
        
        dpapp.storage.getAppStorage('settings')
          .then(function(settings) {
            settings = settings || { clientId: '', clientSecret: '' };
            
            $spanClientId.text(settings.clientId);
            $spanClientSecret.text(settings.clientSecret);
            $form.find('[name="clientId"]').val(settings.clientId);
            $form.find('[name="clientSecret"]').val(settings.clientSecret);
          });
        
        form.on('submit', function(e) {
          e.preventDefault();
          
          var settings  = {};
          var submitted = $(this).serializeArray();
          for (var i = 0; i < submitted.length; i++) {
            var value = submitted[i];
            settings[value.name] = value.value;
          }
          
          dpapp.storage.setAppStorage('settings', settings)
            .then(function() {
              $spanClientId.text(settings.clientId);
              $spanClientSecret.text(settings.clientSecret);
              $indexPage.show();
              $settingsPage.hide();
            });
        });
  
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

The mount point for the app. Initially set to be invisible to hide the contents until the app is ready to run. The children of the element includes two "pages." An index page which displays the app settings, and a settings page with a form which is used to change the settings.

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
$('#settings-btn').on('click', function() {
  $indexPage.hide();
  $settingsPage.show();
});
```

Binds a callback to the settings button `click` event. The callback hides the index page and shows the settings page.

```js
dpapp.storage.getAppStorage('settings')
  .then(function(settings) {
    settings = settings || { clientId: '', clientSecret: '' };

    $spanClientId.text(settings.clientId);
    $spanClientSecret.text(settings.clientSecret);
    $form.find('[name="clientId"]').val(settings.clientId);
    $form.find('[name="clientSecret"]').val(settings.clientSecret);
  });
```

The `dpapp.storage.getAppStorage` function fetches values from DeskPRO app storage, which is a type of key/value store. The code fetches the existing app settings, and updates the index page and settings form with the values.

```js
form.on('submit', function(e) {
  e.preventDefault();

  var settings  = {};
  var submitted = $(this).serializeArray();
  for (var i = 0; i < submitted.length; i++) {
    var value = submitted[i];
    settings[value.name] = value.value;
  }

  dpapp.storage.setAppStorage('settings', settings)
    .then(function() {
      $spanClientId.text(settings.clientId);
      $spanClientSecret.text(settings.clientSecret);
      $indexPage.show();
      $settingsPage.hide();
    });
});
```

Binds a callback to the settings form `submit` event. The callback converts the form values into an object of key/value pairs, and uses the `dpapp.storage.setAppStorage` function to save them. Once saved the index page is updated with the next values, and then the settings page is hidden and the index page displayed.


```js
$appContainer.show();
```

The is fully initialized and all events bound. At this point the app can be unhidden.

## Step 4. Run the dev server
!INCLUDE "_dev_server.md"

## Step 5. Deploy the app
!INCLUDE "_deploy_app.md"
