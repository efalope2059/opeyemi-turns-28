# Game Night

A phone-friendly site for a game night birthday party. Guests check in, play Empire
with names submitted from their own phones, and a leaderboard tracks points across
every game of the night.

It is two static files, so GitHub Pages hosts it for free. Shared state lives in a
Firebase Realtime Database, which is also free at this size.

## Setup

**1. Make the database**

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and create a project (skip Analytics).
2. In the left sidebar pick **Build → Realtime Database**, then **Create Database**. Choose any location and start in **test mode**.
3. Copy the URL at the top. It looks like `https://your-project-default-rtdb.firebaseio.com`.

Test mode lets anyone with the URL read and write, and it expires after 30 days.
That is fine for one party. To set an explicit window, open the **Rules** tab and paste:

```json
{
  "rules": {
    ".read": true,
    ".write": "now < 1800000000000"
  }
}
```

Replace the number with a millisecond timestamp a day after your party.

**2. Fill in config.js**

```js
window.CONFIG = {
  DB_URL: 'https://your-project-default-rtdb.firebaseio.com',
  GM_PIN: '4242',
};
```

**3. Turn on GitHub Pages**

Push this repo to GitHub, then open **Settings → Pages** and set Source to
**Deploy from a branch**, branch `main`, folder `/ (root)`. A minute later the site
is live at `https://<your-username>.github.io/<repo-name>/`.

Make a QR code of that link and stick it on the snack table.

## Running the party

Guests open the link and check in with their name. They see the current game and
the leaderboard.

You open the same link with `?gm` on the end and enter your PIN. That unlocks a
Game master tab.

**Empire.** You open a round and set the prize. Everyone submits one secret name
from their phone, and you can watch who has and hasn't. When you close
submissions, the names are shuffled and your phone shows them one at a time, no
going back. You submit a name too, so you can play. Afterwards you pick the
winner and the points land on the leaderboard.

**Everything else.** The "Other games" box awards points to anyone for any
reason, so Imposter, trivia and a wing-eating contest all feed the same
leaderboard without needing to be built first.

## Things worth knowing

- The PIN is visible in the page source. It stops friends from tapping game master
  buttons by accident; it is not real security.
- The shuffled list of Empire names is held on the game master's phone only, and it
  is wiped from the database the moment reading starts. That is what makes each name
  read exactly once. If you reload on a different phone mid-round, the names are
  gone and you start the round over.
- Points, players and the activity log survive a reload on any phone.
- The page polls every 1.5 seconds. With 20 guests that is well inside Firebase's
  free tier.
- "Wipe all data" at the bottom of the game master tab clears everything for a
  fresh start.

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app: styles, UI and game logic |
| `config.js` | Your database URL and game master PIN |
