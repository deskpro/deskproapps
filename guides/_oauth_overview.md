## OAuth review
Before starting the tutorial let's go over designing DeskPRO apps to use the oauth work flow.

#### 1. Settings
DeskPRO admins must configure the app for oauth on the first run. Configuration is handled by showing the admin a form which has a "Authorization callback URL" value and inputs to enter a client ID and client secret.
![screenshot](/images/guides/oauth-1.png)

#### 2. Registration
The admin is instructed to visit their [GitHub account settings](https://github.com/settings/developers) in order to register the app. The admin will provide the "Authorization callback URL" to the registration form.
![screenshot](/images/guides/oauth-5.png)

#### 3. Credentials
On the next page the admin will be given a client ID and client secret.
![screenshot](/images/guides/oauth-6.png)

#### 4. Saving
The admin comes back to the app settings form where they enter and save the client ID and client secret. This concludes configuring the app for oauth, and the admin may close the window.
![screenshot](/images/guides/oauth-7.png)

#### 5. Authorization
Now when an agent opens the app they will be prompted to authenticate with GitHub.
![screenshot](/images/guides/oauth-4.png)

#### 6. Success
Upon successful authentication the agent will be shown the index page.
![screenshot](/images/guides/oauth-2.png)
