# Role-Based Feature Implementation Plan

## Goal Description

Update the bot to identify if a user is a **Traveler** or a **Guide** and provide features tailored to their role.

## Proposed Changes

### Authentication Logic

#### [MODIFY]

**src/handlers/messageHandler.js**

* Fetch

  **user** record.
* Check `user.role`.
* If `TRAVELER`: Fetch `travelers` record.
* If `GUIDE`: Fetch `guides` record.
* Pass the specific entity (`traveler` or `guide`) and the `role` to commands.

### Feature Updates

#### [MODIFY]

**src/commands/features.js**

* **`!menu`** : Show different options based on role.
* **`!profile`** : Show relevant details (e.g., Guide Rating/NIC for guides).
* **`!trips`** :
  * **Traveler** : Show their active trip.
  * **Guide** : Show assigned trips (Upcoming/Active).
* **New Guide Commands** :
  * `!assignments`: List assigned trips.
  * `!stats`: Show rating and total reviews.

## Verification Plan

1. **Test as Traveler** :
   * Ensure `!menu` shows traveler options.
   * Ensure `!trips` shows traveler's trip.
2. **Test as Guide** :
   * Manually change a user's role to `GUIDE` in DB (or assume a guide number).
   * Ensure `!menu` shows guide options.
   * Ensure `!trips` shows assigned trips.

### Reminder System

#### [NEW]

**src/services/reminderService.js**

* **Dependencies** : `node-cron`.
* **Logic** :

  * Run every hour (or suitable interval).
  * **Trip Start** : Check

    **trips** where `fromDate` is tomorrow.

    * Notify **Traveler** : "Get ready! Your trip starts tomorrow."
    * Notify **Guide** : "Reminder: You have a trip starting tomorrow."
  * **Daily Itinerary** : Check active trips.

    * Notify **Traveler** : "Good morning! Here is your plan for today..." (Link to `!itinerary`).
    * Notify **Guide** : "Day X of Trip Y is starting today."
* **Integration** : Call `initReminders(client)` in

  **src/index.js**.
