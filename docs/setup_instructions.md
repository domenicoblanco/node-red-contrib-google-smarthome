# Setup Instructions

## Prerequisites

- This tutorial assumes your service will be running on https://example.com:3001. Adjust domain, port and paths
  according to your settings.
- You need a 'real' SSL certificate e.g. from [Let’s Encrypt](https://letsencrypt.org/). Make sure you have the public
  and private key file. You can also use a reverse proxy (like Caddy or Traefik) for certificate management and
  SSL termination.
- TCP traffic on port 3001 needs to be forwarded from the Internet to your host where Node-RED is running.

---

## Create project in Actions Console

First we will register a new Smart Home project in the Actions Console.

1.  Go to [Actions on Google Console](https://console.actions.google.com).


2.  Click on *New project*.\
    <kbd>![](images/actionsconsole_new_project.png)</kbd>


3.  Enter a name and select the language and country for your project. Then click *Create Project*.\
    <kbd>![](images/actionsconsole_new_project_form.png)</kbd>


4.  Choose type *Smart Home*, then click *Start Building*.\
    <kbd>![](images/actionsconsole_choose_type.png)</kbd>


5.  Check that your project is selected in the header bar.\
    <kbd>![](images/actionsconsole_check_project.png)</kbd>


6.  Leave the tab *Overview* empty. Switch to tab *Develop* and choose *Invocation* from the sidebar.\
    <kbd>![](images/actionsconsole_tab_invocation.png)</kbd>


7.  Enter your App's name. Click *Save*.\
    <kbd>![](images/actionsconsole_project_name.png)</kbd>


8.  Still on tab *Develop* choose *Actions* from the sidebar.\
    <kbd>![](images/actionsconsole_tab_actions.png)</kbd>


9.  Enter the fulfillment URL. This is the URL of your app, followed by "/smarthome", e.g.
    https://example.com:3001/smarthome. Set the log level to "All". Leave all other fields empty. Then click *Save*.\
    <kbd>![](images/actionsconsole_tab_actions_form.png)</kbd>


10. Still on tab *Develop* choose *Account linking* from the sidebar.\
    <kbd>![](images/actionsconsole_tab_accountlinking.png)</kbd>


11. Fill in the fields as follows:
     * Client ID and secret: Credentials, with which Google will authenticate against your app. Use a password generator
       like https://passwordsgenerator.net/ to generate two strings of reasonable length (e.g. 32 chars). It's
       recommended to not use special characters as they can lead to problems. Copy both strings, you'll need them later.
     * Authorization URL: This is the URL of your app, followed by '/oauth', e.g. https://example.com:3001/oauth.
     * Token URL: This is the URL of your app, followed by '/token', e.g. https://example.com:3001/token.
     * Leave all other fields at their default values.\
   <kbd>![](images/actionsconsole_tab_accountlinking_form.png)</kbd>


12. Click *Save*.\
    <kbd>![](images/actionsconsole_tab_accountlinking_save.png)</kbd>


13. You don't need to fill in anything on the other tabs.


**Note:** You can't test your project in the Action Console's simulator. It only works on real devices.


## Enable Local Fulfillment (optional)

Local fulfillment establishes direct communication between your smart speaker and Node-RED. This reduces latencies
and makes your devices respond faster. It is not required though. If local fulfillment is not setup or is not available, Google will fall back to the
"normal" mode.

1. Open the [Actions on Google Console](https://console.actions.google.com/) and select your project.\
   <kbd>![](images/actionsconsole_check_project.png)</kbd>


2. Click `Develop` on the top of the page, then click `Actions` located in the hamburger menu on the top left.\
   <kbd>![](images/actionsconsole_tab_actions.png)</kbd>


3. Click the button `Upload JavaScript files`.\
   <kbd>![](images/localexecution_upload.png)</kbd>


4. Upload [this Javascript file](https://raw.githubusercontent.com/mikejac/node-red-contrib-google-smarthome/master/local-execution/app.js)
   for both Node and Chrome.\
   <kbd>![](images/localexecution_upload_files.png)</kbd>


5. Tick the `Support local query` checkbox.\
   <kbd>![](images/localexecution_localquery.png)</kbd>


6. Add device scan configuration:
    1. Click `+ New scan config`
    2. Select `MDNS`
    3. set mDNS service name to `_nodered-google._tcp.local`


7. The complete local fulfillment form should look like this.\
   <kbd>![](images/localexecution_form.png)</kbd>


8. `Save` your changes.


## Enable HomeGraph API

Next we will enable the HomeGraph API and download the credentials. This API is used to report the state of your devices
to Google and to inform Google about new or updated devices.

1.  Go to the [Google HomeGraph API page](https://console.cloud.google.com/apis/library/homegraph.googleapis.com).


2.  Check that your project is selected in the header bar.\
    <kbd>![](images/homegraph_project.png)</kbd>


3.  In case you had to switch projects and were redirected away from the HomeGraph API page, open the
    [Google HomeGraph API page](https://console.cloud.google.com/apis/library/homegraph.googleapis.com) again.


4.  Click *Enable*.\
    <kbd>![](images/homegraph_enable.png)</kbd>


5.  From the left sidebar select *Credentials* (or open the
    [API credentials page](https://console.cloud.google.com/apis/api/homegraph.googleapis.com/credentials)). \
    <kbd>![](images/homegraph_sidebar_credentials.png)</kbd>
    

6.  Click *Create Credentials* and select *Service account*.\
    <kbd>![](images/homegraph_create_credentials.png)</kbd>


7.  Enter a name for your service account. A service account ID should be automatically generated.\
    <kbd>![](images/homegraph_credentials_form1.png)</kbd>


8.  Leave steps 2 and 3 empty, just skip them with *Continue* and *Done* buttons.\
    <kbd>![](images/homegraph_credentials_form2.png)</kbd>
    <kbd>![](images/homegraph_credentials_form3.png)</kbd>


9.  Find your newly create service account in the list and click on the edit button.\
    <kbd>![](images/homegraph_serviceaccount_list.png)</kbd>


10. Switch to the tab *Key*.\
    <kbd>![](images/homegraph_serviceaccount_tab_key.png)</kbd>


11. Click on *Add Key*, then *Create Key*.\
    <kbd>![](images/homegraph_serviceaccount_key_create.png)</kbd>


12. Choose type *JSON* and click *Create*.\
    <kbd>![](images/homegraph_serviceaccount_key_json.png)</kbd>


13. A JSON file containing the JWT key will be downloaded to your computer. Copy it to your Node-RED server, in a
    location where the Node-RED service can read it.


## Install and configure Node-RED module

Now we will install the module in Node-RED and configure it.

1. Install `node-red-contrib-google-smarthome` from Node-RED's palette and restart Node-RED.\
    <kbd>![](images/nodered_install.png)</kbd>


2. Place the Management node from the section "Google Smart Home" on a flow.\
    <kbd>![](images/nodered_management.png)</kbd>


3. Edit the management node and open its config. Fill in the fields as following:
    * Name: A name for your config node.
    * Enable Node Debug: Check this box if you want to see the debug messages from the node. Enable it for now.
    * Default Language: The language of your project.
    * Use Google login: Check, if you want to use authentication via Google Sign-In. This tutorial uses the simpler
      password based authentication, so disable it.
    * Username/Password: Username and password of your choice. You use this to log in with the Google Home App later.
    * Client ID and Client Secret: The client ID and secret you entered in the Google Actions Console earlier.
    * Jwt Key: Path to the JSON file you downloaded while enabling the HomeGraph API. Can be an absolute path or a path
      relative to Node-REDs user dir (where your settings.js, flows.json etc. are stored).
    * Port: The port on which the service should run. If left empty, the service will run on the same port as Node-RED This port must be reachable from the internet.
      (but take care to protect your Node-RED from external access when you expose this port to the internet).\
      This tutorial assumes port 3001, so set it to 3001.
    * Path: URL path on which the service will run. If set, it will be https://example.com:3001/<yourpath>/smarthome.
      If left empty, https://example.com:3001/smarthome will be used.\
      This tutorial assumes a simple path setup, so leave it empty.
    * Use http Node-RED root path: If enabled, the service will respect the setting "httpNodeRoot" in Node-RED's
      settings.js. If set, the service will use https://example.com:3001/<httpNodeRoot>/smarthome. If there is also a
      path set in the Path field, the service will use https://example.com:3001/<httpNodeRoot>/<yourpath>/smarthome.
      This tutorial assumes a simple path setup, so leave it empty.
    * Use external SSL offload: If enabled, the smarthome service will use HTTP instead of HTTPS. Check this box if you
      want to do SSL termination on a reverse proxy like Caddy or Traefik.
    * Local fulfillment port: A port number to use for local fulfillment. Choose any port you want. This port is used
      for internal communication only and must not be forwarded externally.
    * Public and Private Key: Path to public and private key files of your SSL certificate (if you do not use external
      SSL decryption). Remember that you need to restart Node-RED after the certificates are renewed.
    * Access Token Duration, Report Interval, Request sync delay, set_state message delay: Usually you don't need to
      change these values. Leave them at their default values.\
    <kbd>![](images/nodered_configuration.png)</kbd>
    

4. Deploy the flow.\
   <kbd>![](images/nodered_deploy.png)</kbd>


5. Check if your service is reachable. Open https://example.com:3001/check in your browser. You should see the
   "Google SmartHome test page". You don't need to fill in anything here, if you see the page, you are good to go.\
   <kbd>![](images/nodered_check.png)</kbd>


6. Also check if the page is reachable from the internet. Use a tool like https://reqbin.com to send a GET request to
   https://example.com:3001/check (using your domain name and port). It must answer with status 200 and the message
   "SUCCESS: Smart Home service is reachable!".\
   <kbd>![](images/nodered_reqbin.png)</kbd>


## Setup Account linking

Finally, we will link the Google Home App to the Node-RED service.

Note: Screenshots were taken on a german phone. If you can provide screenshots on an english phone, please open an issue
or a pull request.

1. Open the Google Home App on a device logged into the same account used to create the project in the Actions Console.


2. Click the '+' sign to add a device.\
   <kbd>![](images/phone_plus.png)</kbd>
   

3. Click *Set up device*.\
   <kbd>![](images/phone_setupdevice.png)</kbd>


4. Click *Have something already set up*.\
   <kbd>![](images/phone_existingdevice.png)</kbd>


5. Find your app in the list of providers. It will be marked with `[test]`.\
   Note: Old projects will stay on this list, even after they are deleted. That's why I have several Demo projects.\
   <kbd>![](images/phone_project.png)</kbd>


6. Log in to your service. Username and password are the ones you specified in the configuration node.\
   <kbd>![](images/phone_login.png)</kbd>


8. Say "Hey Google, sync my devices". Google should answer that your project was successfully synced.


9. Congratulations! Your project is successfully set up. You can now start adding devices.


## Finalizing local fulfillment

If you enabled Local fulfillment earlier, you need to:
- restart Node-RED
- wait 30 minutes or restart your Google device

You can test if local fulfillment was successfully enabled by saying "Hey Google, force local" (works on non-english
devices too). Then try to control your devices. All actions will now be executed locally or will fail if local
fulfillment is not available. After you are done testing, revert to normal mode by saying "Hey Google, force default".

Local fulfillment can be tricky to set up. If you have problems, look at the troubleshooting section in the readme.


## Integrating Google Sign-In (optional)

Your project was created using username/password based authentication. You can switch to authentication using
Google Sign-In if you want. This makes relinking your project in the app a little easier as you don't have to enter
your username and password again. For regular users it is not necessary though.

<span style="color: red">Currently, we are experiencing issues with Google Sign-In.

1.  Navigate to the [GCP oAuth consent screen configuration](https://console.cloud.google.com/apis/credentials/consent).


2.  Check that your project is selected in the header bar.\
    <kbd>![](images/homegraph_project.png)</kbd>


3.  If you haven't configured the OAuth consent screen yet, you will be asked for the user type. Choose "External" and
    click "Create".\
    <kbd>![](images/googlesignin_type.png)</kbd>


4.  In case you already had your consent screen configured earlier, there's a link "Edit App" next to the title which
    will open the configuration form again.\
    <kbd>![](images/googlesignin_editapp.png)</kbd>

5.  Enter a name for your project, select your e-mail address from the list and at the bottom of the form enter your
    e-mail again as developer contact. Add your domain "example.com" (without protocol and port) as authorized domain.
    Then click "Save and continue".\
    <kbd>![](images/googlesignin_consentform.png)</kbd>


5.  In the next two steps "Scopes" and "Optional info" leave all fields empty and click "Save and continue".


6.  On the last step you'll see a summary of your settings. Check that it looks like in the screenshot. Especially check
    that your domain is added as authorized domain.\
    <kbd>![](images/googlesignin_consentform_summary.png)</kbd>


7.  Select "Credentials" on the left sidebar.\
    <kbd>![](images/googlesignin_sidebar_credentials.png)</kbd>


8.  Click "Create credentials" and choose OAuth client ID.
    <kbd>![](images/googlesignin_createcredentials.png)</kbd>


9.  Choose "Web application" as application type. Enter a name for your project. As "Authorized JavaScript Origin" enter
    the URL of your service without a path (https://example.com:3001). As "Authorized redirect URI" add the URL of your
    service with the path "/oauth" (https://example.com:3001/oauth). Then click "Create".\
    <kbd>![](images/googlesignin_createclientid.png)</kbd>


10. Copy the client ID. You will need it later.\
    <kbd>![](images/googlesignin_copyclientid.png)</kbd>


11. When you come back to this step later and need the client ID again, you can copy it from the table "OAuth 2.0 Client
    IDs". Check that you are copying from the correct row.\
    <kbd>![](images/googlesignin_copyclientid_later.png)</kbd>


11. Open the configuration of the Google management node in Node-RED. Check "Use Google login" and enter the client ID
    you copied earlier. Add your email address (the one your Google account is registered to) as authorized email. Save
    and deploy your flows.\
    <kbd>![](images/googlesignin_nodered.png)</kbd>


12. Re-link your account as described in the section [Setup Account Linking](#setup_account_linking). Instead of
    username and password you will have the button "Sign in with Google".\
    <kbd>![](images/googlesignin_phone.png)</kbd>
