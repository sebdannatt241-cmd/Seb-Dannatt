GRAVEYARD SMASH — ONLINE MULTIPLAYER MVP
========================================

WHAT'S IMPLEMENTED IN THIS PACKAGE
- Preserves the existing Netlify site, direct index.html -> game.html navigation, and all local game modes/rules.
- Adds a guest-friendly online lobby to game.html: create private room, share a short room code, join a friend's room, open a public room, and search for a public room by mode.
- Adds Supabase Realtime room updates and shared game-state synchronization for the existing turn-based engine.
- Supports Classic Clash (Teams and Free-for-all), Siege Tactics, Horde Survival, and Boss Battle.
- Uses a browser-generated guest ID and nickname; online guest play does not require an account. Existing email account UI remains optional.
- Does not clear or migrate local progress, achievements, cosmetics, or the local leaderboard.
- Adds multiplayer_rooms SQL to supabase_setup.sql. Existing leaderboard SQL is retained.

IMPORTANT: IMPLEMENTED VS. LIVE CONFIGURATION
The front-end multiplayer UI and client integration are implemented in this package. Online multiplayer will NOT work until the SQL below is applied to the intended Supabase project and Realtime is enabled for multiplayer_rooms. This package has not been deployed to Netlify or tested against a live Supabase backend from this environment.

BACKEND SETUP
1. Open your Supabase project dashboard.
2. Go to SQL Editor -> New query.
3. Paste and run the ENTIRE supabase_setup.sql file in this package (it includes the existing leaderboard setup and the multiplayer room table/policies).
4. In Database -> Replication / Publications (dashboard wording can vary), confirm public.multiplayer_rooms is included in the supabase_realtime publication. The SQL attempts to add it automatically.
5. Open config.js and set window.GRAVEYARD_SUPABASE.url and .anonKey to your project's Project URL and publishable/anon key. Never put a service_role or secret key in browser code.
6. In Supabase Authentication -> URL Configuration, add your deployed Netlify origin to the allowed redirect URLs if you want email sign-in too.
7. Deploy the extracted contents of this folder to your existing Netlify site, keeping index.html, game.html, config.js, supabase_setup.sql, netlify.toml and assets/ together.
8. Test with two separate browsers/devices: create a private room in one, join its code in the other, and start a match. Also test public rooms separately for each mode.

HOW TO PLAY ONLINE
- Open the game and scroll to ONLINE GRAVEYARD on the setup screen.
- Enter a nickname and choose a mode.
- For friends, create a private room and share the code. Your friend enters the code and selects Join room.
- For public play, choose Open public match to create a public waiting room, or Find public match to join an existing waiting room for the selected mode.
- The room host starts the match once at least two players have joined.

KNOWN MVP LIMITATIONS / SECURITY
- This is a functional prototype, not a hardened competitive multiplayer service. The guest-friendly SQL policies intentionally permit anonymous clients to read and modify room rows. Anyone with the public anon key can potentially alter room contents or game state, and room codes are not authentication.
- Do not use this policy design for ranked play, paid tournaments, or valuable rewards. Before a public competitive launch, replace broad anonymous table write access with trusted Edge Functions/RPCs, authenticated player membership, server-validated actions, rate limits, expiry/cleanup for abandoned rooms, and authoritative match result calculation.
- Browser disconnects and simultaneous updates can cause race conditions; use a host-authoritative server protocol for a production release.
- Existing local achievements/cosmetics/progress remain local and are not automatically merged or synchronized to the online guest ID. Account-based cross-device sync remains a separate feature.
- The existing email auth/leaderboard can work once its schema and auth settings are configured, but the multiplayer MVP does not require signing in.
- The code was syntax/packaging checked only; a live end-to-end multiplayer test requires the configured Supabase project and deployment.
