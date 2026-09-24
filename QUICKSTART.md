# GitPerennial Quickstart

**Originator & Architect:** Kirk Shallcross - Shallcross Consulting  
**Implementation Specialist:** Anthropic Claude AI / Claude Code  
**Inception Date:** September 2026  
**Version:** 1.0.0

This is an **operational guide** — how to get GitPerennial installed
and running, and how to actually use it day to day once it's set up:
what the Dashboard shows, what Settings controls, and what the
Archive page is for. For the pitch, pricing, and licensing details,
see [README.md](README.md).

**All three platforms — Windows, macOS, and Linux — have been fully
built, tested on real hardware, and are ready to release.**

---

## Getting installed

Full detail (the macOS security prompts you'll see during setup and
what each one means, .NET runtime, tray-icon quirks per Linux desktop,
upgrading, uninstalling) is in
[README.md's install sections](README.md#installing-on-windows) — this
is the fast path. On macOS specifically, see
[What to expect during setup](README.md#installing-on-macos) before
you install — it walks through the 2-3 standard security prompts macOS
shows for any app like this, so none of them catch you off guard.

1. Download the installer for your OS from the
   **[Releases page](https://github.com/SC-Admin567/GitPerennial/releases)**:
   `GitPerennial.msi` (Windows), `GitPerennial.pkg` (macOS), or
   `gitperennial.deb` (Linux, Debian/Ubuntu family).
2. Run it and follow the installer. It sets up the app, registers a
   background service that keeps polling GitHub for you, and adds a
   tray/menu-bar icon.
3. Click the **GitPerennial** icon in your system tray (Windows/Linux)
   or menu bar (macOS) → **Open Dashboard** — or just browse to
   `http://localhost:5000`. (On Linux, whether a tray icon appears
   depends on your desktop — see the
   [tray-icon notes](README.md#installing-on-linux). The dashboard works
   the same either way.)
4. In **Settings**, paste in a GitHub Personal Access Token (`repo`
   scope) and add the repo(s) you want to track. See
   [README.md's token section](README.md#setting-up-your-github-token)
   for the exact scope requirements.

**Reading this file outside GitHub, without nice Markdown rendering?**
The installer also adds a **GitPerennial Documentation** icon (Desktop
and Applications menu/Launchpad) that opens this same repository
directly on GitHub — always the current README and Quickstart,
rendered properly, no separate reader needed.

Once a token and at least one repo are set up, GitPerennial polls
GitHub automatically (4×/day) and starts building history immediately
— GitHub's own traffic API returns up to 14 days of backfill on the
very first poll, so you're not starting from zero.

### Linux: handy terminal commands

Run these as your own user — **not** with `sudo`, because GitPerennial's
background service is a per-user service.

| To… | Run |
|---|---|
| See whether the service is running | `systemctl --user status gitperennial` |
| Start or stop it | `systemctl --user start gitperennial` / `systemctl --user stop gitperennial` |
| Read its log | `journalctl --user -u gitperennial` |
| Check the .NET runtime is installed | `dotnet --list-runtimes` (look for a `Microsoft.AspNetCore.App 10.x` line) |
| Check it starts at boot | `loginctl show-user $USER --property=Linger` (should say `Linger=yes`) |
| Reach the dashboard on a headless server | `ssh -L 5000:localhost:5000 you@your-server`, then browse to `http://localhost:5000` on your own machine |
| Uninstall | `sudo apt remove gitperennial` (your data in `~/Documents/GitPerennial/` is kept) |

### Where does the installer put things, and can I choose?

There's no "choose install location" step in any of the three
installers — each one always installs to the same fixed folder for
that OS, and that's by design (it's what makes an in-place upgrade
safe and keeps the app easy to find):

| OS | The app itself installs to | Your data lives in |
|---|---|---|
| Windows | `C:\Program Files\GitPerennial\` | `Documents\GitPerennial\` — everything, including your database |
| macOS | `/Applications/GitPerennial/` | `~/Documents/GitPerennial/` — everything, including your database |
| Linux | `/opt/gitperennial/` | `~/Documents/GitPerennial/` — everything, including your database |

**These are two completely separate locations, and that's intentional,
the same way on all three platforms.** The left column is just the
*program* — the `.exe` (or Linux/macOS binary) and its supporting
files, the same on every machine, replaced whenever you install a
newer version. The right column is *your* data — your actual SQLite
database, backups, CSV exports, saved print files, and logs — created
the first time you run GitPerennial, and never touched by an install,
an upgrade, or an uninstall.

Because the program's location is always the same fixed folder, an
upgrade always finds your existing database automatically — there's
nothing to move, relink, or re-choose. If you ever need to fully
remove GitPerennial (see each OS's uninstall steps in
[README.md](README.md)), only the left-column *program* files are
removed; your actual data — wherever it lives per the table above —
is left alone on purpose.

---

## Finding your way around

### The Dashboard (the page you land on)

Your home screen — one card per tracked repo, at a glance:

- Each card shows the repo's name, a **Private** badge if it's a
  private repo, and a sync-status indicator (healthy, delayed, or
  data-loss) showing when it last successfully checked in with
  GitHub.
- Click any card to open that repo's detail page.
- If a repo has become unreachable on GitHub (deleted, made private,
  or your token lost access), a notice names it — its history is kept
  either way; you decide when to remove it from tracking in Settings.
- If your GitHub token is expiring soon, has expired, or was
  rejected, a colored notice tells you and points you to Settings to
  fix it.
- If a newer version of GitPerennial is available, a dismissible
  banner links to the release notes.

### A repo's detail page (click a card to get here)

Five tabs, each covering a different slice of that repo's history:

- **Traffic** — clone and view counts over time (total and unique),
  as two line charts. Toggle "Show data table" under either chart to
  see the exact numbers. Any day with no real data (e.g. GitHub
  hasn't published today's numbers yet, or your computer was off) is
  shown as part of the same continuous line, colored differently
  (like a traffic-delay overlay on a map) rather than left blank or
  broken — hover the marker at the start of a colored stretch for
  exactly why, and the footnote below the chart lists every one.
  GitPerennial never guesses at missing data, it flags it honestly
  instead.
- **Referrers & Content** — which external sites are sending you
  traffic, and which pages/files in your repo get the most views,
  each as a "top 5 over time" chart with the rest available in a
  table.
- **Releases** — one section per published release: version tag,
  publish date, prerelease/draft badges, and a download-count chart
  per attached file. Each file's name is shown with its release tag
  alongside it (e.g. `MyApp.msi (v1.0.0)`) — if you ever delete and
  re-upload the same file while editing a release on GitHub, its
  download counts are combined into one continuous line rather than
  showing up as a separate, duplicate entry.
- **Issues** — open vs. closed issue counts over time.
- **Events** — your own notes. Add a title, date, an optional link to
  a specific release, a description, and pick which chart(s) it
  should appear on. A tagged event shows up as a dashed vertical line
  on that chart — hover it to see the note. Use this to mark things
  like "launched on Product Hunt" or "v2.0 released" so you can see
  the effect on your traffic later.

**Export CSV / Print Charts / Print Report** buttons appear on most
tabs (wording varies slightly by tab). All three open a date-range
picker first:
- **Export CSV** downloads that tab's data as a spreadsheet file for
  your own analysis.
- **Print Charts** opens a clean, chart-only page ready to print or
  save as a PDF.
- **Print Report** opens the same period as a formatted table/report
  (with totals), also ready to print or save as a PDF.

Every print you make is automatically saved to your **Archive** (see
below) as a permanent, timestamped record — even if you only meant to
print it once. This is a deliberate audit-trail feature, not a bug:
it means a printed report from three months ago can never be
silently altered by later corrections to your data.

**We recommend Chrome for printing** — see [README.md's Printing
reports section](README.md#printing-reports) for why, and a known
issue with landscape table reports in some other browsers.

### Settings

Everything about how GitPerennial is configured, in four sections:

- **Tracked repos** — add a repo by typing `owner/repo-name`, remove
  one (this keeps its full history — "remove" is not "delete"), and
  if you're licensed, restore a previously removed repo from the
  "Removed repos" list.
- **GitHub token** — shows whether a token is set and its health, and
  a field to paste in a new one when it's time to rotate it.
- **License** — shows your current status (free tier or licensed),
  how long the app's been running, and everything license-related:
  activating a key, reactivating a saved key, renewing for a new major
  version, moving your license to another computer (and importing it
  there), seeing which of your (up to 2) computers are activated, and
  a link to email support if you've genuinely lost a key.
- **Data locations** — plain-English descriptions of exactly where
  your database, backups, logs, exports, print files, and exported
  license transfer files live on disk (all under your own Documents
  folder, on every OS — see "Where does the installer put things, and
  can I choose?" above), and what gets cleaned up automatically vs.
  kept forever. This data survives an uninstall — reinstalling doesn't
  lose anything.

### Archive

A browsable history of every CSV export and print you've ever made —
your permanent audit trail, not a temporary download folder.

- Two tables: **Saved Print Files** and **Exported CSV Files**.
- Filter either one by **Type** (traffic, referrers, releases, etc.)
  and a **From/To** date range — GitPerennial tells you plainly if a
  range is invalid rather than silently correcting it for you.
- Click any column header to sort; page through results (10 / 20 /
  50 / 100 / All).
- Click a file name to open it — print files open right in your
  browser; CSV files open in a simple built-in table viewer.
- Select one or many rows (checkbox, or "select all" for whatever's
  currently visible) and **Delete Selected** to clean up old records
  in bulk, or delete one at a time from its own row.
- A footer at the bottom shows the real folder path for each archive
  and the total size of everything stored there, so you always know
  how much disk space your history is using.

---

## What happens if your computer is off for a while

GitPerennial catches up automatically the next time it starts. On
Linux the installer also turns on "linger" for your account, so the
background service starts when the machine boots — even before anyone
logs in — and a reboot that sits at the login screen doesn't pause
collection. If the gap is under 14 days, GitHub's own rolling history covers it — you
won't lose anything. If the gap is longer than 14 days with the
machine completely off the whole time, that stretch is genuinely gone
(GitHub itself no longer has it) — the dashboard will say so plainly
rather than pretend nothing happened.

---

Questions or something not working? See the
[FAQ in README.md](README.md#faq), or reach out at
**gitperennial-support@shallcross-consulting.com**.
