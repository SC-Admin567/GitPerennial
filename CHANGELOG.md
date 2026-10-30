# Changelog

**Originator & Architect:** Kirk Shallcross - Shallcross Consulting  
**Implementation Specialist:** Anthropic Claude AI / Claude Code  
**Inception Date:** September 2026  
**Version:** 1.1.0

All notable changes to GitPerennial are recorded here, newest first.
Dates are in `YYYY-MM` format.

This file lists user-facing changes only — what's new, fixed, or
different for someone running the app. It does not track internal
development history (see the private development repository for
that). Starting with the next release, entries here follow the usual
shape for a changelog — short, grouped bullets describing what
changed since the prior version. This first entry is written a little
differently, since there's no prior version to diff against; it's as
much a launch note as a changelog entry.

## [1.1.0] — 2026-09

**Multiple GitHub accounts.** A GitHub Personal Access Token is
inherently limited to a single resource owner — one personal account,
or one organization — so if you personally own repos under more than
one GitHub account (a second personal account, an organization, or an
enterprise's organization), there was previously no way to track
repos across more than one of them at once. GitPerennial now supports
configuring a separate token per owner, and automatically uses the
matching one based on each repo's owner.

- New **GitHub accounts** section on the Settings page (moved above
  Tracked repos, since you now add an account before adding its
  repos). Each configured owner shows as its own row — a status line
  (token health, expiration), a **Manage** control to replace or
  remove that owner's token, and, for a fine-grained token, a note
  that new repos need to be added to the token's own repository access
  list on GitHub before GitPerennial can track them.
- Adding an account checks the pasted token with GitHub right away and
  shows which account it belongs to, so you can confirm it's the right
  one before saving. If a fine-grained token was scoped to an
  organization rather than the personal account it was issued under,
  an "Owner name" field (shown only when it could apply — never for a
  classic token) lets you name that organization directly.
- A classic token automatically works for any repo it has access to —
  its own account's repos, plus any organization it's a member of — so
  you generally only need one entry per classic token, even if it
  reaches repos under more than one GitHub owner. A fine-grained token
  is scoped to exactly the owner and repos it was given access to.
- Adding a repo now gives a specific reason when it can't be tracked:
  no account configured yet for that repo's owner, that owner's token
  not having push access, or — for a fine-grained token — the token
  existing but not yet covering that particular repo.
- Replacing an account's token now checks every repo already tracked
  under that owner against the new token, and warns by name if any of
  them are no longer reachable — most often because a narrower
  fine-grained token replaced a classic one. This catches the problem
  at the moment it's introduced, rather than as a confusing, unexplained
  sync failure later.
- **Upgrading from 1.0.x:** your existing token is migrated
  automatically into this new format on first launch, under the
  account it already belonged to. No re-entry needed, no interruption
  to repos already being tracked.
- The free tier is unchanged — still exactly one tracked repo,
  forever, regardless of how many GitHub accounts you configure.

**Linux: now also available as a `.rpm` for Fedora.** The same
GitPerennial 1.1.0, packaged for Fedora, with a `.rpm` installer
alongside the existing `.deb`. It has been tested on Fedora 44 — on
KDE Plasma (install, reinstall, upgrade with your data kept, and
removal) and on GNOME (tray icon with the extension below). The
background service and tray icon work the same as on the `.deb`.

- **GNOME needs an extension for the tray icon.** GNOME has no
  built-in system tray, so the tray icon only appears once the
  "AppIndicator and KStatusNotifierItem Support" extension is installed
  and enabled. (KDE Plasma and most other desktops need nothing extra.)
  If GNOME is detected without it, GitPerennial now shows a one-time
  notification pointing to the steps in the README and QUICKSTART, and
  the installer prints the same hint. This applies to both the `.rpm`
  and the `.deb`. GitPerennial itself runs normally either way — only
  the tray icon is affected.

**The Traffic tab now matches GitHub Insights; the other tabs are
snapshots.**

Only the Traffic tab (clones and views) mirrors GitHub's Insights →
Traffic graphs, because GitHub supplies a real day-by-day series for the
last 14 days. Everything else (Referrers, Content, Releases, Issues) is
a *snapshot* (only the current day's figures, not a running 14-day
series): GitPerennial records one reading per poll day and builds the
history itself from those readings.

- The Traffic tab shows the same 14 days as GitHub's Insights → Traffic
  graphs (ending yesterday, UTC), so the numbers can be compared day for
  day. Print and export dialogs open on the dates shown on screen.
- Fixed false "the app or computer was likely off" notes on the
  Referrers, Content and Releases charts: a referrer, page, or release
  file that simply wasn't in GitHub's list on a polled day is no longer
  treated as missing data. Only days with no poll at all are flagged.
- When a poll really was missed, the note under the chart now explains
  how to catch up right away (tray icon menu → Stop Service, then Start
  Service, which polls immediately).
- Reports, CSV exports and long-range printed charts are now accurate.
  GitHub reports referrers and popular content as rolling 14-day totals;
  these used to be added together day after day (a referrer with 12 views
  could print as 83). Each period now shows the latest figure, with no
  misleading Total rows (same fix for Issues). A day or period with no data
  shows a dash (blank in CSV) instead of 0, and every report, printed
  chart and CSV now ends with notes explaining the figures and listing any
  days with missing data and why.
- Release download totals now match GitHub's release page: only files
  currently on the release are counted; files since removed or renamed on
  GitHub keep their history in rows marked "removed from GitHub" but are
  not added into the total. The release report, CSV export and printed
  release chart use the same figures.
- Release reports and printed release charts now cover any date range:
  every release published on or before the end of the range is included,
  with downloads as of the end of the range and the number gained during
  it (previously they printed "No data" unless a release was published
  inside the printed dates). Printed release charts now mirror the
  dashboard (one chart per release, one line per file), and printed Clones
  and Views charts show the Total and Unique lines, with overlaps printed
  as alternating black and gray dashes that read on a black-ink printer.
- Referrers & Content reports are compact: one side-by-side table (a row
  per period, with the combined figure and the top 5 entries) replaces a
  separate table for every entry, with a short latest-figures table for
  the rest.
- The Referrers & Content CSV now includes every referrer and page (it
  used to hold only the combined total per period): a flat table of period,
  referrer or page, count and uniques for every entry with traffic in the
  range.
- No more false "the computer was off" notes when GitHub's referrer or
  page list goes empty (entries aging out of its 14-day window): GitPerennial
  now checks whether the repo was actually polled, and those days show 0.
- The dashboard's "Show data table" views now match the charts and reports:
  a day with no data reads "—" instead of 0, and the Referrers & Content
  table shows the figures from the window being viewed.
- Only totals that make sense are shown: unique-visitor columns no longer
  have a total (one person on three days is three daily uniques), and
  combined uniques across referrers or pages are removed; day counts and
  release downloads keep their totals.
- CSV exports are cleaner: ISO period start/end dates on every row, a Notes
  section naming the repository, date range and export time, blank cells
  for missing data, correct display in Excel (UTF-8 marker), and no empty
  Events section.
- CSV exports keep page paths and event titles exactly as they are (they
  used to have "/" rewritten to "_").
- Polling: a referrer or page GitHub drops from its list later the same
  day no longer lingers with an old count, and a popular page with no
  title no longer fails that repo's poll.
- Lines that share the same value are drawn as alternating dashes in
  their legend colors instead of hiding one another, and each chart has a
  short caption explaining what it shows.
- Print Charts now work in black and white (distinct dash patterns and
  marker shapes, shown in the legend, plus colors that print as clearly
  different grays), carry the same captions, and no longer need an
  internet connection.

**Also fixed (all platforms):**

- The background service now reliably restarts after an upgrade or
  repair install on Windows — it could previously stay silently
  stopped until the next reboot or a manual start.
- The tray icon now appears immediately after install or upgrade, on
  all three platforms — previously it could require logging out,
  restarting, or a manual launch before it showed up.
- The installer now clearly shows which version you're installing —
  in the Windows Setup wizard, the macOS Installer wizard, and Linux's
  `dpkg`/`apt` install output.
- macOS: the uninstaller now confirms which version it's removing.
- Windows: fixed a rare startup race where the background service,
  if it started before anyone had logged in (e.g. after an overnight
  reboot), could open the wrong, empty-looking database for that
  session instead of your real one. The service now correctly finds
  your real data on the very first start, with no manual step needed.

## [1.0.0] — 2026-09

**First public release.**

GitPerennial was built with cross-platform parity as a real
requirement from the start, not an afterthought bolted on once
Windows was done. Each platform's background service, system tray
(or menu-bar) integration, and installer were built against that
platform's own native mechanisms — a Windows Service, a macOS
launchd daemon, a systemd user service on Linux — rather than a
lowest-common-denominator shim. Every installer runs the app once in
normal interactive mode before ever registering the background
service, specifically so the database and file paths get set up
correctly as the actual person running it, not as a system account
with no access to that person's own files. Windows, macOS, and Linux
have all now been validated end-to-end on real hardware — a genuine
fresh install, an in-place upgrade, and (on macOS and Linux) the full
licensing flow, including moving an active license from one computer
and operating system to a different one, all confirmed working. Linux
has additionally been confirmed to survive a real reboot with no one
logged in. One narrow item remains open on macOS: confirming that its
"runs in the background" notification only ever appears once per
install rather than repeating.

**What's included:**

- Self-hosted GitHub traffic & release analytics — clones, views,
  referrers, popular content, release download counts, and issue
  metrics — retained forever, outlasting GitHub's own 14-day traffic
  history.
- Free tier: track one repository forever, at full feature depth, no
  time limit.
- Perpetual license (one-time purchase) to track additional
  repositories, plus an optional annual renewal to unlock future major
  versions.
- Local dashboard with per-repo Traffic, Referrers & Content, Releases,
  Issues, and Events tabs, all backed by Chart.js visualizations. On
  the Releases tab, a file you delete and re-upload while editing a
  release on GitHub is combined into one continuous download-history
  line/column labeled with its release's version (e.g.
  `MyApp.msi (v1.0.0)`), instead of appearing once per upload.
- CSV export and printable chart/report views for every tab, with a
  permanent, timestamped Archive of everything you've exported or
  printed.
- Your own manual annotations (Events) overlaid directly on the
  relevant charts.
- Runs entirely on your own machine with your own SQLite database and
  your own GitHub personal access token — nothing about your repos or
  traffic is ever sent anywhere else.
- **Windows:** MSI installer, background Windows Service, system tray
  icon. Your database lives in your Documents folder
  (`Documents\GitPerennial\gitperennial.db`), matching macOS and Linux
  exactly, rather than inside the install folder.
- **macOS:** `.pkg` installer, background launchd service, menu-bar
  icon. *(Fully tested on real hardware: install, upgrade, uninstall,
  free-tier limit, full license activation/renewal, and moving an
  active license in from a Linux install. Still open: confirming
  macOS's background-activity notification fires only once per
  install.)*
- **Linux:** `.deb` package for Debian/Ubuntu and derivatives,
  background systemd `--user` service (starts at boot, before anyone
  logs in), system tray icon (where supported by the desktop
  environment). *(Tested on Ubuntu 24.04 with the MATE desktop: fresh
  install (including the .NET runtime being installed automatically),
  in-place upgrade, removal, start-at-boot after a real reboot,
  collecting real GitHub data, and license activation, including
  moving a license in from a file. Other desktop environments, such as
  GNOME, have not been tested yet.)*
