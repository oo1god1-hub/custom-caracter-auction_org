# Custom Character Auction (updated)

A real-time multiplayer character auction built with Node.js, Express and Socket.IO. Supports up to 3 players per room.

## Features
- Game password required on every fresh page load. The game password is verified server-side; the browser keeps only an in-memory access token.
- Separate server-side admin login.
- 15,000 coins per player.
- Default character roster and tier starting bids: SSS 1,000; SS 800; S 500; A 300; B 100.
- 16 default characters including Neha (A tier). Existing power ratings from the source project are retained for the original 15. Neha is provisionally assigned 75 power; change it in `defaultCharacters` in `server.js` if desired.
- Quick bid with a player-controlled increment (default +50); manual bids remain available.
- Every accepted bid adds 5 seconds to the current countdown.
- Side-by-side team panels with character thumbnails, tiers and remaining budgets. Team power is withheld until the auction ends.
- Host-only pause/resume.
- End-of-game results, team power comparison, spending, remaining budget, highest-value purchases and full auction history.
- Admin panel to upload/replace default character images, add custom characters and remove custom characters. Default characters cannot be deleted.
- Character images upload to Cloudinary as authenticated assets.

## Local setup
1. Install Node.js 18 or newer.
2. Run `npm install`.
3. Set environment variables (see below).
4. Run `npm start` and open `http://localhost:3000`.

## Required environment variables
Set these in your hosting provider's environment-variable settings. Do not commit secrets to source control or share them in chat.

```text
GAME_PASSWORD=dada1234yuvi
ADMIN_PASSWORD=varsha69
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
DATA_DIR=/var/data
```

`ADMIN_PASSWORD` has no insecure built-in fallback. The admin panel stays locked until it is configured. The requested value is `varsha69`; set it only in Render Environment Variables. Because it has been shared in chat and is a simple password, strongly consider replacing it with a longer unique password before deployment.

## Render deployment and persistence
- Create a Node web service from this project ZIP/repository.
- Build command: `npm install`
- Start command: `npm start`
- Add the environment variables above in Render.
- Attach a Render Persistent Disk and mount it at `/var/data`; keep `DATA_DIR=/var/data`. This stores the custom-character roster metadata across restarts/deploys. Without a persistent disk, metadata stored locally may be lost when the instance is replaced.
- Cloudinary stores the image assets separately. Configure all three Cloudinary variables before using photo uploads.

## Image privacy note
Images are uploaded as Cloudinary `authenticated` assets and delivered using signed URLs. Signed URLs are access credentials while valid: anyone who obtains a signed URL may be able to view that image. This reduces public discoverability but cannot prevent screenshots, copying, or sharing by an authorized viewer.

## Operational notes
- Rooms, player connections, bids and active auction state are held in server memory. A server restart will end active rooms; this build does not provide durable reconnect/resume of an auction after a server process restart.
- Pause/resume is available to the room host during an active auction.
- Admin additions are available in rooms created after the character is added; an already-running room uses the roster snapshot it started with.
- Game/admin access tokens are in-memory and are invalidated by a server restart. A page refresh requires entering the game password again.
