<img width="128" alt="slack-archive-logo" src="./logo/icon.svg">

# Export your Slack workspace as static HTML

Alright, so you want to export all your messages on Slack. You want them in a format that you
can still enjoy in 20 years. This tool will help you do that.

 * **Completely static**: The generated files are pure HTML and will still work in 50 years.
 * **Everything you care about**: This tool downloads messages, files, and avatars.
 * **Nothing you do not care about**: Choose exactly which channels and DMs to download.
 * **All types of conversations**: We'll fetch public channels, private channels, DMs, and multi-person DMs.
 * **Incremental backups**: If you already have local data, we'll extend it - no need to download existing stuff again.
 * **JSON included**: All data is also stored as JSON, so you can consume it with other tools later.
 * **No cloud, free**: Do all of this for free, without giving anyone your information.
 * **Basic search**: Offers basic search functionality.

<img width="1151" alt="Screen Shot 2021-09-09 at 6 43 55 PM" src="https://user-images.githubusercontent.com/1426799/132776566-0f75a1b4-4b9a-4b53-8a39-e44e8a747a68.png">

## Using it

1. Make sure you have [Node.js](https://nodejs.org/en/) installed, ideally something newer than Node v14.
2. Download and run a _temporary_ installation of the package, through the mighty `npx`[^1] runner:

   ```sh
   npx slack-archive
   ```
3. Feed your [User OAuth Token](#getting-a-token) to the prompt and let the program interactively guide you through all the options.
4. The exported data will be stored, in nice HTML format, inside the `./slack-archive` subdirectory. If the subdirectory already exist the program will compare the available workspace data with its content and download and merge only the newer additions.

[^1]: NPX is an acronym for Node Package Execute. It comes with NPM, the Node Package Manager. 
NPX has the ability to execute a Node package which wasn't previously installed, downloading it on the flight from the NPM registry.

### Parameters

```
--automatic: Don't prompt and automatically fetch all messages from all channels.
--no-backup: Don't create backups. Not recommended.
--no-search: Don't create a search file, saving disk space.
```

## Getting a token

In order to download messages from private channels and direct messages, we will need a "User OAuth Token" associated with you and the target workspace. Slack uses this token to identify what permissions it'll give this app. We used to be able to just copy a token out of your Slack app, but now we'll need to create a custom app, install it to the target workspace and retrieve the token from its Slack API tab.

This will be mostly painless, I promise.

### 1) Configure your custom app

- Head over to https://api.slack.com/apps and sign in to your account.
- Press the `Create New App` button and select the `From an app manifest` option.
- Choose the workspace you'd like to backup with slack-archive.
- When prompted for an App Manifest, just paste in the following[^2] yaml configuration:
  ```yaml
  display_information:
    name: Slack-Archive_<username>
    description: Export user-visible channel data as static HTML. Incrementally.
    background_color: "#de0446"
  features:
    bot_user:
      display_name: Slack-Archive
      always_online: false
  oauth_config:
    scopes:
      user:
        - channels:read
        - channels:history
        - files:read
        - groups:read
        - groups:history
        - mpim:read
        - mpim:history
        - im:read
        - im:history
        - users:read
      bot:
        - commands
        - chat:write
        - chat:write.public
  settings:
    org_deploy_enabled: false
    socket_mode_enabled: false
    token_rotation_enabled: false
  ```
- Replace `<username>` with whatever identifier you prefer (e.g. `your-surname`); please notice that this app-name will be visible to all the workspace members, in the `Apps` tab of the Slack UI.
- Proceed and confirm the summary: your custom app is ready!
 
[^2]: It can also be found as a single file in the root folder of the repo, if you want to be sure not to mess with whitespace (as it is crucial for yaml syntax). Look for `slack-archive.yaml`.

### 2) Authorize

- Select `Install to Workspace` at the top of the app page (or `Reinstall to Workspace` if you have done this previously).
- You will be prompted to an authorization page, review the permissions (they should match what you have configured in the yaml manifest).
- Then, from the `Features` menu on the left of the app page, select `OAuth & Permissions`: there you will find your **User OAuth Token**, which will generally be in `xoxp-********...` format. This is the token you need to past into the slack-archive prompt, in order for the program to work.

