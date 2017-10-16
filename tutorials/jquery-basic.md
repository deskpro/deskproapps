Overview
========
This tutorial will walk you through creating a simple DeskPRO app using jQuery. The app will display a list of people participating in a ticket.

![screenshot](/images/tutorials/basic-1.png)

----

!INCLUDE "_prerequisites.md"

----

## Step 1. Clone the boilerplate
The SDK boilerplate includes the basic app configuration and files to help developers get started writing apps.

```
git clone https://github.com/deskpro/deskproapps-boilerplate-react jquery-tutorial
cd jquery-tutorial
npm install
```

## Step 2. Update the manifest
Edit the app configuration in _package.json_, which can be found in the app root directory. Change the "title" property to "Participants".

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

**Explanation**  

*  The "title" value will be shown in the app toolbar. See the [manifest documentation](/api/manifest.md) for more information.

## Step 3. Modify the HTML
Edit the app HTML file to look like the following.

{% label %}src/main/html/index.html{% endlabel %}
```html
<!DOCTYPE html>
<html lang="en" class="deskpro-sidebar">
  <head>
    <style type="text/css" media="all">
      @import "../assets/main.css";
    </style>
  </head>
  <body>
    <div id="deskpro-app" class="deskpro-app dp-container">
      <ul id="participants" class="participants-list"></ul>
    </div>
    
    <script src="https://code.jquery.com/jquery-3.2.1.js"></script>
    <script src="../assets/vendor.js"></script>
    <script src="../assets/main.js"></script>
    
    <script>
      function deskproapp(dpapp) {
        var participants = $('#participants');
        dpapp.context.getTabData()
          .then(function (resp) {
            var tabData = resp.api_data;
            
            for (var i = 0; i < tabData.participants.length; i++) {
              var item = tabData.participants[i];
              var li = $('<li/>')
                .appendTo(participants);
              $('<img/>', {
                'class': 'dp-avatar dp-avatar--l',
                'src':   item.person.default_picture_url
              }).appendTo(li);
              $('<div/>')
                .html(item.person.name)
                .appendTo(li);
              $('<div/>')
                .html(item.person.primary_email.email)
                .appendTo(li);
            }
          });
      }
    </script>
  </body>
</html>
```

**Explanation**  

* An unordered list is added to the page `<ul id="participants" class="participants-list"></ul>`, which will contain the list of participants.
* The SDK calls `function deskproapp(dpapp)` when it's been fully loaded. The `dpapp` argument is an object which will be used to communicate with the DeskPRO API.
* We grab a reference to the unordered list by calling `var participants = $('#participants')`. We will append list items to the list. One for each participant.
* The `dpapp.context.getTabData()` method fetches information about the opened ticket. It returns a promise which eventually resolves to the data.
* A `for` loop is used to iterate over each person participating in the opened ticket.
* A list item `li` is created and appened to the unordered list for each participant which contains their avatar, name, and primary email address.

## Step 4. Modify the app styles
Modify the stylesheet to look like the following.

{% label %}src/main/sass/index.scss{% endlabel %}
```sass
$dp-styles-font-path: "~deskpro-components/src/styles/fonts/";
@import "~deskpro-components/src/styles/main.scss";

.participants-list {
  list-style-type: none;
  
  li {
    margin-bottom: 1rem;
  }
}
```

## Step 5. Run the dev server
!INCLUDE "_dev_server.md"

## Step 6. Deploy the app
!INCLUDE "_deploy_app.md"
