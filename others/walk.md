# WhatsApp Bot Walkthrough

## Overview

The WhatsApp bot has been fully integrated with the database and now supports role-based features for **Travelers** and **Guides** , along with an automated **Reminder System** .

## Features Implemented

### 1. Database Integration

* **Schema** : Updated to match the application's data model (

  **users**, `travelers`, `guides`,

  **trips**,

  **events**, etc.).
* **Connection** : Switched to a connection pool for stability.

### 2. Role-Based Authentication

* The bot automatically detects if a user is a **Traveler** or a **Guide** based on their phone number.
* **Travelers** : Access their active trip, itinerary, and events.
* **Guides** : Access their assigned trips and profile stats.

### 3. Rich Feature Commands

* **`!itinerary`** : Displays a detailed day-by-day plan with:
  * 📸 Images of places.
  * 📍 Location pins for easy navigation.
  * 📝 Descriptions and durations.
* **`!events`** : Lists upcoming events with photos, prices, and location pins.
* **`!profile`** : Shows user details specific to their role (e.g., Guide Rating).

### 4. Automated Reminders

* **Trip Start** : Notifies both Traveler and Guide 24 hours before a trip starts.
* **Daily Itinerary** : Sends a "Good Morning" message to Travelers with a prompt to check their daily plan.

## Verification Steps

### Prerequisites

1. Ensure the database is running and `DATABASE_URL` is set in

   **.env**.
2. Run `pnpm start` to launch the bot.

### Manual Testing

#### Traveler Flow

1. **Login** : Send any message from a registered Traveler phone number.
2. **Menu** : Send `!menu`. Verify you see "My Trips", "My Itinerary", etc.
3. **Itinerary** : Send `!itinerary`. Verify you receive a sequence of messages with images and location pins.
4. **Events** : Send `!events`. Verify you see a list of events with details.

#### Guide Flow

1. **Login** : Send any message from a registered Guide phone number.
2. **Menu** : Send `!menu`. Verify you see "My Assignments".
3. **Assignments** : Send `!assignments` (or `!trips`). Verify you see a list of upcoming trips assigned to you.

#### Reminders (Simulation)

To verify reminders immediately, you can temporarily modify the cron schedule in

**src/services/reminderService.js** to run every minute (`* * * * *`) and adjust the date logic to match a test trip in your database.

## Conclusion

The bot is now a robust companion for both travelers and guides, providing timely information and rich media content directly within WhatsApp.
