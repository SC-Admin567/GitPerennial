# Changelog

**Originator & Architect:** Kirk Shallcross - Shallcross Consulting  
**Implementation Specialist:** Anthropic Claude AI / Claude Code  
**Inception Date:** September 2026  
**Version:** 1.0.0

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
