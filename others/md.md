# WhatsApp Bot Walkthrough

## Changes Implemented

* **Refactored Command Handler** : Commands are now dynamically loaded from the `commands` directory, making it easier to add new features.
* **General Commands** :
  * `!help`: Lists all available commands.
  * `!owner`: Displays bot owner information.
* **Group Commands** :
  * `!promote @user`: Promotes a user to admin.
  * `!demote @user`: Demotes an admin.
  * `!kick @user`: Removes a user from the group.
  * `!revoke`: Revokes the group invite link.
  * `!tagall`: Mentions everyone in the group.
  * `!groupinfo`: Shows detailed group information.
* **Media Commands** :
  * `!sticker`: Converts an image/video/gif to a sticker.
  * `!toimg`: Converts a sticker to an image (experimental).

## Verification Steps

### 1. Restart the Bot

Since the bot was running with `node` (not `nodemon`), you must restart it to apply the changes.

1. Stop the current process (Ctrl+C).
2. Run `pnpm start`.

### 2. Test General Commands

* Send `!help` to the bot.
* Verify it lists all the new commands.
* Send `!owner` and check the response.

### 3. Test Group Commands

* Add the bot to a test group.
* **Promote/Demote** : Mention a user with `!promote @user` and `!demote @user`.
* **Tag All** : Send `!tagall` and check if everyone is mentioned.
* **Info** : Send `!groupinfo`.

### 4. Test Media Commands

* **Sticker** : Send an image with the caption `!sticker`. The bot should reply with a sticker.

### 5. Test Contact & Profile Commands

* **Contact** : Send `!contact @user` or `!contact` (for self).
* **Profile Pic** : Send `!pfp @user`.
* **My Profile** : Send `!myprofile`.

### 6. Test Chat Management

* **Mute** : Send `!mute` and check if the bot replies.
* **Clear** : Send `!clear` (Be careful, this clears chat).

### 7. Test Group Settings

* **Restrict** : Send `!restrict` in a group (Bot must be admin).
* **Announcement** : Send `!announcement` in a group (Bot must be admin).

### 8. Test Interactions

* **React** : Quote a message and send `!react 🚀`.

### 9. Database Setup

* **Environment** : Add `DATABASE_URL=postgres://user:password@host:port/dbname` to your

  **.env** file.
* **Dependencies** : Run `pnpm add drizzle-orm pg` if you haven't already.
* **Restart** : Restart the bot with `pnpm start`.

### 10. Test Database Features

* **Auth** : Try sending a command from an unregistered number (should fail).
* **Menu** : Send `!menu` to see options.
* **Profile** : Send `!profile`.
* **Trips** : Send `!trips`.
* **Itinerary** : Send `!itinerary`.
* **Payments** : Send `!payments`.
* **Events** : Send `!events`.
