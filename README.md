# Discord Server Tag Automation Script (GUILD_TAGS)

Recently, around May 6th, 2024 (YYYY/MM/DD: 2024/05/06), Discord began allowing servers to potentially receive the `GUILD_TAGS` feature flag upon creation. This occurs randomly with a small chance (approximately 1 in 1000). This script attempts to automate the process of repeatedly creating and deleting servers until one with this specific feature is found by leveraging functions exposed via Vencord.

> [!CAUTION]
> Using automated scripts and client modifications like Vencord violates Discord's Terms of Service. This can lead to **account suspension or permanent banning.**

Before running the script, ensure you have completed the following steps:

1.  Disable 2FA (Required but Risky): The script needs 2FA disabled to automatically delete temporary servers without requiring confirmation prompts. Re-enable 2FA after you finish using the script.
2.  Install Vencord: This script relies on functions exposed by the Vencord client modification. Download and install it from the official source.
3.  Enable Vencord Plugin: Open Discord Settings, go to the Vencord "Plugins" tab, find and enable the **"ConsoleShortcuts"** plugin. This plugin exposes useful library functions (like `findByProps`) to the developer console, which are necessary for this script to work.
4.  Restart Discord Client: Close and reopen Discord completely to ensure Vencord and the plugin are loaded correctly.
5.  Create One Server Manually (Required): Create a single server through the normal Discord interface first.

## Instructions

1.  Open Developer Console: Use the standard browser/Electron method to open the developer console (usually `Ctrl+Shift+I` on Windows/Linux or `Cmd+Option+I` on Mac, or sometimes F12).
2.  Allow Pasting (If Prompted): The console might require you to explicitly type `allow pasting` before accepting pasted code. **Be extremely cautious** about pasting code from untrusted sources into your console, as it can compromise your account.
3.  Paste and Run Code: Copy the entire JavaScript code block below, paste it into the console, and press Enter:

```js
const INTERVAL = 5 * 1000; // Time between attempts in milliseconds (e.g., 5000 = 5 seconds)
                           // Lower intervals increase creation speed but also ban risk significantly.

const deleteGuild = findByProps(
  "deleteGuild",
  "bulkAddMemberRoles"
).deleteGuild;
const createGuildFromTemplate = findByProps(
  "createGuildFromTemplate"
).createGuildFromTemplate;

let t = setInterval(async () => {
  const newGuild = await createGuildFromTemplate(
    "Tag server",
    null,
    {
      id: "CREATE",
      label: "Create My Own",
      channels: [],
      system_channel_id: null,
    },
    false,
    false
  );

  let shouldDelete = true;
  for (const feature of Array.from(newGuild.features)) {
    if (feature === "GUILD_TAGS") {
      shouldDelete = false;
      break;
    }
  }

  if (shouldDelete) {
    console.log("Deleting guild:", newGuild.id);
    await deleteGuild(newGuild.id);
  } else {
    console.log(`FOUND GUILD WITH TAG: ${newGuild.id}`);
    clearInterval(t);
  }
}, INTERVAL);
```

**Remember to re-enable 2FA on your account once the script has found a server or you decide to stop it.**
