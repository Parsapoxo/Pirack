# Changelog

All notable changes to **Pirack** are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html).

> **Note on history:** versions up to 3.0.2 were reconstructed after the fact when the
> project switched from its old custom numbering to SemVer. They carry no dates. The old
> custom number (where one existed) is shown in parentheses. From 4.0.2 on, every release is
> added to this file as it is made, with its release date (YYYY-MM-DD).
>
> **Note on numbering:** the maintainer set the site version label to 4.0.2 directly after
> 3.0.2. Versions 3.0.3 to 4.0.1 were never released.

## [Unreleased]

### Added
- `gif2video.py`: a command-line tool (Python + ffmpeg) that converts GIF banners to `.mp4` or
  `.webm`, keeping the original GIFs. Typically cuts file size by 80% or more.

## [4.7.0] - 2026-10-19

### Added
- Admin panel, Leaderboard tab: under each player's Prize field there is now a line showing
  **Spent** and **Available** (Prize minus purchases), with a breakdown of titles, banners,
  avatars, and personal-photo uploads. It is read from Supabase and updates as the Prize is
  edited; the new "↻ Refresh balances" button reloads it.

### Changed
- The Prize field is now labelled "total earned" and a note explains that purchases are
  subtracted automatically and never change the Prize number saved in `leaderboard.json`.

## [4.6.1] - 2026-10-19

### Fixed
- Video banners (`.mp4` / `.webm`) in the Item Shop stayed black. Videos now request their
  first frame (`#t=0.001`) and carry the `autoplay` attribute, a "Loading…" label shows while
  a banner loads, and a failed video shows a visible error with its code (and logs the file
  path to the browser console) instead of an empty black card.

## [4.6.0] - 2026-10-19

### Added
- Live game-card preview in the admin panel. Each game has a collapsible preview that mirrors
  the card on the site (poster, badges, genres, players, size, buttons) and updates as fields
  change, including poster upload and removal. Notes beside the card show where the poster and
  trailer come from, how the ⬇ button will behave, and which buttons will be hidden.

## [4.5.0] - 2026-10-19

### Changed
- Redesigned the leaderboard top 3. Rank 1 gets a large full-width card; ranks 2 and 3 sit
  side by side as smaller cards. Every card shows the player's banner (5:1, GIF, mp4 and webm
  supported), the avatar overlapping the banner, name, player title, score, and a medal badge,
  with gold, silver, and bronze frames. Players without a banner get a tinted placeholder.
- Player names on the podium are now HTML-escaped.

### Removed
- The old three-column podium and its CSS.

## [4.4.0] - 2026-10-19

### Added
- Announcement history. All announcements stay available behind the bell, newest first, each
  with a "Published X ago" note (full date and time on hover).
- Unread blinking announcements are marked with a NEW label. The red dot stays until the
  visitor opens the bell; "seen" state is stored per browser.
- Admin panel: the Announcement tab is now a list with "+ New announcement" and a Delete
  button per entry.

### Changed
- `announcement.json` is now a list of announcements (`id`, `title`, `message`, `mode`,
  `createdAt`). The single-message format from 4.3.0 is still read and converted.
- Editing an existing announcement keeps its original publish time and does not re-trigger
  the red dot.

### Removed
- The "Clear message" button (replaced by per-announcement Delete).

## [4.3.0] - 2026-10-19

### Added
- Announcement bell between the menu button and the profile button. Opening it shows a
  message with a large bold title and text below.
- Two modes, set from the new Announcement tab of the admin panel: Blinking shows a red dot
  on the bell until the visitor opens it; Normal shows no dot, but the message stays available.
- New data file `announcement.json`, created on first save from the admin panel.

### Changed
- The profile button moved one slot to the left to make room for the bell.

## [4.2.2] - 2026-10-19

### Security
- `supabase-hardening.sql` (run manually in the Supabase SQL Editor, no site code change):
  turns on Row Level Security for `player_data`, blocks deleting rows, validates player slugs,
  limits list sizes and text lengths, and adds a guard so that owned items cannot be removed,
  `avatar_spend` can only increase (by at most 1500 per update), and `player_slug` cannot
  change. Anyone can still impersonate a player, because login is checked in the browser.

### Fixed
- Purchases were not atomic (read, change, write), so two tabs or devices buying at the same
  time could lose an item or miscount avatar spend. Adding an owned item and adding avatar
  spend now run as single database operations (`supabase-atomic.sql`, functions
  `pirack_add_item` and `pirack_add_spend`).
- Selecting a title, banner, or avatar now writes only that column, so a stale copy can no
  longer overwrite newer data.
- The latest player data is re-read right before a purchase, and extra clicks are ignored
  while a purchase is running.
- If the SQL functions are not installed yet, the site falls back to the old method.

## [4.2.1] - 2026-10-19

### Added
- Banners can be `.mp4` or `.webm` files (muted, looping, paused while off-screen). The admin
  panel accepts video uploads for banners and previews them.

### Fixed
- Item Shop banners loaded slowly and animated GIFs stuttered. The first banners are preloaded
  when the shop opens (or when the Item Shop card is hovered), in parallel with the Supabase
  request. The first six banners load with high priority and the rest lazily. The shimmer
  animation stops once a banner has loaded, and GIFs outside the viewport are swapped for a
  still frame so only visible ones animate. The same applies to leaderboard banners.

## [4.2.0] - 2026-10-19

### Added
- Animation when switching between Games, Apps, and Leaderboard: cards fade and slide in one
  after another, coming from the side that matches the tab order. It is skipped while typing
  in search and for visitors who prefer reduced motion.

### Changed
- Clicking the tab that is already open no longer re-renders the list or clears the search.

## [4.1.1] - 2026-10-19

### Fixed
- The red indicator in the Games / Apps / Leaderboard tab bar was off-center on Apps and
  Leaderboard (a rounding error that grew with each tab). Its width is now exactly one third
  of the inner width.
- Tab columns could become unequal when a label was wide; they are now always equal.
- The tab bar and the Main Menu button now share the same maximum width and side margins.
- Smaller text on very narrow phones so "Leaderboard" fits.

### Changed
- Tabs now expose `tablist` / `tab` roles and `aria-selected`, have a visible keyboard focus
  outline, and no tap highlight on mobile.

## [4.1.0] - 2026-10-19

### Added
- Trailer links can point to Aparat (page links, `videohash` links, or pasted iframe code),
  YouTube, or Vimeo; they are converted to an embedded player automatically. Direct video
  files still use the normal player, and any other link is tried in an iframe. An "Open in a
  new tab" link is shown under embedded players.

## [4.0.2] - 2026-10-19

### Added
- Trailer URL field for each game in the admin panel (stored as `trailer` in `games.json`).
  When it is empty, the ▶ button still plays `trailers/<game-slug>.mp4`.

### Changed
- Version label set to v4.0.2. From here on, versions follow Semantic Versioning strictly:
  PATCH for fixes, MINOR for new backward-compatible features, MAJOR for breaking changes.

## [3.0.2]

### Fixed
- Item Shop banners loaded slowly and sometimes stuttered. Banner and avatar images now use
  lazy loading and async decoding, off-screen cards are not rendered until needed, and a
  shimmer placeholder shows while an image loads. Removed the per-frame hover zoom that
  stressed animated GIFs. (old 8.12)

## [3.0.1]

### Changed
- Item Shop visual polish: gradient cards with hover lift and glow, divider lines on section
  headers, a highlighted Prize status bar, animated Coming Soon panels, and a soft entrance
  animation. (old 8.11)

## [3.0.0]

### Changed
- **BREAKING:** live player data (owned and selected titles, banners, avatars, avatar spend)
  moved from Firebase Firestore to Supabase (`player_data` table), because Firebase is
  blocked for users in Iran. Existing Firestore data was not migrated. (old 8.10)

## [2.18.0]

### Added
- Multi-part downloads: the ⬇ field in the admin panel takes one URL per line. One URL opens
  directly; several URLs open a dropdown with Part 1, Part 2, and so on.

### Changed
- Buttons whose link is empty (Steam, PirateGames, ⬇) are now hidden instead of disabled.
- Menu button is back at the top-right, with the profile button (rounded square) to its left.
  The wallpaper drawer slides in from the right again. (old 8.9)

## [2.17.0]

### Added
- Avatars tab in the Item Shop (renamed from Profiles) with ready-made avatars that players
  can buy. Managed from a new Avatars tab in the admin panel with categories, name, price,
  tier color, and file path (stored in `profilepics.json`).

### Changed
- Uploading a personal profile photo now costs 1500 Prize, charged when the request is sent.
  A purchased avatar overrides the personal photo. (old 8.8)

## [2.16.0]

### Changed
- Prize is no longer shown on the leaderboard (only in the Item Shop). Player name, player
  title, and score are stacked in that order. (old 8.7)

## [2.15.0]

### Added
- Profiles tab enabled in the Item Shop (personal photo upload with admin approval).

### Changed
- The avatar now sits flush against the left side of the banner. (old 8.6)

## [2.14.0]

### Added
- Equipped banners are shown in leaderboard rows for ranks 4 and below: a 5:1 banner with a
  1:1 avatar attached to it. Players without a banner keep the plain row. (old 8.5)

## [2.13.0]

### Changed
- Prize is now a manual per-player field in the admin panel, independent of Score. Players
  without a manual value fall back to the old Score × 5. (old 8.4)

## [2.12.0]

### Added
- Banner categories in the admin panel. Each banner's color is limited to the five tier
  colors used by Player Titles.

### Changed
- `banners.json` now has the shape `{ categories, items }`. The old flat array is still
  read. (old 8.3)

## [2.11.0]

### Added
- Banners in the Item Shop and the admin panel: PNG or GIF files, always displayed at 5:1,
  with a per-banner name and price. GIFs keep animating. Purchases use the shared Prize
  balance. (old 8.2)

## [2.10.1]

### Fixed
- Item Shop card now has a pointer cursor and a hover animation. The Main Menu button matches
  the Item Shop card in size and has a softer hover transition. (old 8.1)

## [2.10.0]

### Added
- Item Shop with Profiles, Player Titles, and Banners tabs, a Main Menu button, and an
  animated tab swap.
- Player Titles: 5 tiers with 10 titles each (Champion 1000, Legendary 800, Epic 600,
  Rare 400, Common 200; Champion has an animated color). Players can buy titles and pick the
  active one from their profile.
- Titles are shown under the player name on the leaderboard, and leaderboard rows were
  redesigned. (old 8.0)

## [2.9.0]

### Added
- Firebase Firestore connected as the store for live player data. (old 7.4)

## [2.8.3]

### Fixed
- Replaced the expired Issues token. (old 7.3)

## [2.8.2]

### Changed
- Configured the Issues-only token used for avatar requests. (old 7.2)

## [2.8.1]

### Changed
- Wallpaper drawer slides in from the left. (old 7.1)

## [2.8.0]

### Added
- Sign in with username (the player name) and a password set in the admin panel. Signed-in
  players cannot change their username or password.
- Profile button and modal with a crop tool for a new profile picture. The picture is sent as
  a GitHub Issue and approved or rejected in the new Avatar Requests tab of the admin panel.

### Changed
- Menu button moved to the top-left. (old 7.0)

## [2.7.2]

### Fixed
- Leaderboard podium overflowed on narrow portrait phones. (old 6.2)

## [2.7.1]

### Changed
- Faster uploads from the admin panel (native base64 encoding). Manual file-path fields added
  for files uploaded directly on GitHub. (old 6.1)

## [2.7.0]

### Added
- Hamburger menu with a wallpaper picker: the default live wallpaper plus images or videos
  added from a new Wallpapers tab in the admin panel. Each visitor's choice is remembered in
  their browser. (old 6.0)

## [2.6.1]

### Fixed
- Search bar and Item Shop card now have the same height, so nothing shifts when switching
  tabs. (old 5.8)

## [2.6.0]

### Added
- Version label in the bottom-right corner.
- Item Shop placeholder card (Coming Soon) shown on the Leaderboard tab. (old 5.7)

## [2.5.0]

### Added
- File Password line with a copy-to-clipboard button.

## [2.4.0]

### Added
- ⬇ download button on game cards.

### Changed
- Download button renamed to PirateGames. Admin link fields grouped together.

## [2.3.4]

### Changed
- Inter font used site-wide. Removed the yellow Prize pill on leaderboard rows.

## [2.3.3]

### Fixed
- Avatar crop now outputs a square image instead of a circle.

## [2.3.2]

### Fixed
- Admin panel shows the real reason when an upload fails, and retries automatically on GitHub
  version conflicts.

## [2.3.1]

### Fixed
- A score typed as 60 could be saved as 57.

## [2.3.0]

### Added
- Leaderboard tab with a top-3 podium and a ranked list, `leaderboard.json`, a Leaderboard
  tab in the admin panel, and avatar upload with a crop tool.

## [2.2.0]

### Added
- "New" flag for games: flagged games are pinned first and get a green badge.

## [2.1.0]

### Added
- Reorder arrows for games and apps in the admin panel.

## [2.0.0]

### Added
- Admin panel (`admin.html`) that edits the site through the GitHub API.

### Changed
- **BREAKING:** game and app data moved out of `index.html` into `games.json` and
  `apps.json`, which are loaded at runtime.

## [1.2.0]

### Added
- Games / Apps tab switcher with a sliding animation, and an Apps section with downloads.

## [1.1.2]

### Added
- Seven Borderlands games, with their posters wired in.

## [1.1.1]

### Changed
- Favicon, redesigned header (centered logo, red title), larger logo, tighter spacing, and
  background grid alignment.

## [1.1.0]

### Added
- Self-hosted trailers: the ▶ button plays `trailers/<game-slug>.mp4`.

### Removed
- Steam widget trailer embed (it redirected to Steam instead of playing).

## [1.0.0]

### Added
- First stable release: published on GitHub Pages and shared with friends.

## [0.4.0]

### Added
- Custom posters for 13 games, loaded from the `posters/` folder.

## [0.3.0]

### Added
- ▶ trailer button and modal (Steam widget).

## [0.2.0]

### Changed
- Red and gold theme, grid-line background, three cards per row, and wide 16:9 posters.

## [0.1.0]

### Added
- First version: game grid with Steam CDN posters, search, dark theme, animated particle
  wallpaper, English UI, and the Pirack name.
