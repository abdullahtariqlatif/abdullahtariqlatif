# Setup Notes

This folder is its own git repo, separate from the Career Archive. Built 22nd-October-2026,
based on `https://github.com/Shoaib19/Shoaib19`'s README structure as a starting template, then
rewritten with your real info (pulled from `identity.md`, `skills.md`, and `general-summary.md`
in the Career Archive) and your real public repos (confirmed via the GitHub API, not guessed).

## The repo already exists on GitHub

`github.com/abdullahtariqlatif/abdullahtariqlatif` already exists - empty, created today
(2026-10-22). GitHub auto-offers this when you create a repo named exactly as your username; it
seems you already did that step. No need to use `gh` or create anything on github.com - just
push to it.

## Push it

```bash
cd "/home/abdullahtariq/ACoreFolders/MainFolders/site/Other Files/github-profile-readme"
git init
git add README.md NOTES.md
git commit -m "Add GitHub profile README"
git branch -M main
git remote add origin https://github.com/abdullahtariqlatif/abdullahtariqlatif.git
git push -u origin main
```

HTTPS push will ask for your GitHub username + a Personal Access Token (not your password -
GitHub removed password auth for git operations). If you don't have a token yet: GitHub ->
Settings -> Developer settings -> Personal access tokens -> generate one with `repo` scope.
SSH works too if you already have an SSH key set up with GitHub - just use
`git@github.com:abdullahtariqlatif/abdullahtariqlatif.git` as the remote instead.

## To actually show private commits too

The stats/streak cards in the README (`github-profile-summary-cards`, `streak-stats`) and the
green contribution graph GitHub shows on your profile page both read from your *public*
contribution graph by default - private-repo commits don't count unless you turn on:

**github.com/settings/profile -> scroll to "Contributions" -> check "Include private
contributions on my profile."**

That's an account setting, not something the README's code can control - nothing to push for
this part, just toggle it once.

## What's deliberately left out of v1

Shoaib19's original template also had an animated snake game (eats your contribution squares),
an isometric 3D contribution view, and a `github-metrics.svg`. All three need a scheduled GitHub
Action in this repo to generate and commit an image file back to an `output` branch - more
moving parts (workflow permissions, sometimes a PAT secret) than the zero-setup embed APIs used
above. Skipped for now to keep the first push simple and reliable. Say the word if you want one
added later - the typing header, stats cards, trophy, and visitor counter above all needed zero
repo configuration and will keep working as-is.

## Activity badges

The Contributions/Commits/Current Streak/Longest Streak badges under "⚡ Activity" are pushed
into this `README.md` by an external automated process on a schedule - replacing the content
between `<!--COMBINED-STATS:START-->` / `<!--COMBINED-STATS:END-->`. This repo doesn't contain
that process; it only ever receives the finished result. No action needed here for that to keep
working.

## Tech Stack icons vendored locally; Contra link removed (22nd-October-2026)

The Tech Stack grid used to link straight to `raw.githubusercontent.com/devicons/devicon/...`
for all 20 icons - same risk you flagged: an external repo's files can move, get renamed, or
(worst case) if that repo were ever compromised, GitHub would be serving whatever that attacker
put at that URL directly into your profile page. Downloaded all 20 SVGs into `icons/` in this
repo instead (devicon is MIT-licensed, vendoring is allowed) and pointed the README at the local
copies (`icons/ruby.svg`, etc.) - this repo now owns every byte it displays for that section,
nothing fetched from `devicons/devicon` at render time.

**What's still external, and why that's unavoidable:**
- The stats/streak/trophy/metrics/snake/isometric images are *dynamically generated* (they
  reflect live GitHub activity) - those can't be "downloaded once," by definition. Where
  possible we already generate them ourselves into this repo (snake, 3D-contrib, and metrics all
  commit their own output here, not someone else's server; activity badges are pushed in by an
  external process - see "Activity badges" above). The two still-external ones (Stats/Languages
  cards, and the trophy mirror) are documented in their own sections below as known, accepted
  tradeoffs.
- The small colored label badges (LinkedIn, Gmail, LeetCode, CodeWars) are `img.shields.io` -
  these aren't icon artwork, they're text+color rendered on the fly by a widely-trusted badge
  service (used in the overwhelming majority of GitHub READMEs). Left as-is since there's
  nothing meaningful to "download" - say the word if you'd rather these be plain text/links
  instead.

**Contra link removed** from the social badges row (`https://abdullahtariqbutt.contra.com/`) -
freelance-portfolio links don't belong next to a profile recruiters will check against a resume.

## Trophies: the official widget is down, swapped to a volunteer mirror (22nd-October-2026)

`github-profile-trophy.vercel.app` started returning `HTTP 402 Payment required /
DEPLOYMENT_DISABLED` - confirmed by curling it directly, not a local network issue. Checked the
project (`ryo-ma/github-profile-trophy`)'s own README: the maintainer posted a notice that
rising traffic made the free shared instance unsustainable, and lists several volunteer-run
mirror URLs as a stopgap while asking people to sponsor or self-host.

**Correction:** this tool isn't Action-renderable like the snake game or 3D contributions - it's
a live web service (a Next.js app on Vercel), not something a scheduled GitHub Action can
generate into a static file. "Self-host via Action" (what was first proposed) doesn't apply
here; true self-hosting means forking the project on GitHub and deploying that fork to your own
free Vercel account - a one-time manual step outside this repo, not something to script.

**Current fix:** tested every mirror listed in the upstream README directly - most are dead
(404) or erroring (500). Two are alive and returning real trophy SVGs:
- `https://trophygithubreadmelang.cybee.dpdns.org` - **in use now**
- `https://trophy.ryglcloud.net` - backup if the first one also goes down later

If both die, check `ryo-ma/github-profile-trophy`'s README again for a fresher mirror list, or
fork+deploy your own Vercel instance for full independence from any shared instance (search
their README for "Reducing server load through self-hosting").

## Known limitation: Stats/Languages cards are public-repos-only (22nd-October-2026)

You flagged the Stats/Languages row looking off. Checked each card's raw output directly -
confirmed, not guessed:

- **Stats card is wrong and can't be fixed as-is.** It showed "Total Commits: 17," which is far
  too low. That number comes from GitHub's commit Search API, which only indexes a repo's
  default branch and only for repos the *widget's own server token* can see - that token has
  zero access to your private repos, regardless of the "Include private contributions on my
  profile" toggle. That toggle only makes the contribution-calendar total public - it does not
  grant any third party actual read access to private repo content, which is what Total
  Commits/Stars/PRs/Issues on that card all need.
- **Languages card, same root cause.** Shows only your public repos' languages. Any private
  repo's language is invisible to it for the same reason.

**Not fixed, by your choice:** fixing Top Languages across private repos would need a real
`repo`-scope Personal Access Token (actual private-repo content read access, not just a
visibility toggle) - a meaningfully bigger privilege grant than anything else this repo uses.
Decided 2026-10-22: accept the public-only limitation rather than grant that.

## Three more "fancy" additions (22nd-October-2026)

### Snake game (`.github/workflows/snake.yml`)
Official recipe from `Platane/snk`'s own maintainer profile repo. No PAT needed - default
`GITHUB_TOKEN` is enough. Runs on push to `main`, daily schedule, or manual trigger. Writes
`github-contribution-grid-snake.svg`/`-dark.svg` to a separate `output` branch (the action
creates that branch itself the first time it runs) - that's why the README embed points at
`.../output/github-contribution-grid-snake.svg`, not `main`. **Will 404 until the workflow has
run at least once** - trigger it manually (Actions tab -> "generate animation" -> Run workflow)
after pushing, don't wait for the daily schedule.

### Isometric 3D contributions (`.github/workflows/profile-3d.yml`)
Official recipe from `yoshi389111/github-profile-3d-contrib`'s own README. No PAT needed -
default `GITHUB_TOKEN` is enough (same private-contributions toggle you already enabled applies
here too). Commits 10 theme variants straight into `profile-3d-contrib/` on `main` - the README
currently embeds `profile-night-rainbow.svg`; the other 9 are listed in that Action's README if
you want to swap the theme later. **Doesn't trigger on push** (schedule + manual only) - trigger
it manually once (Actions tab -> "GitHub-Profile-3D-Contrib" -> Run workflow).

### GitHub Metrics (`.github/workflows/metrics.yml`)
Official recipe from `lowlighter/metrics`. **This one genuinely needs a Personal Access Token**
- its own docs say the default `GITHUB_TOKEN` can't fetch the activity/user data this plugin
set needs. One-time setup before this workflow will succeed:
1. github.com -> Settings -> Developer settings -> Personal access tokens (classic) -> Generate
   new token. **No scopes need to be checked** for the default output - leave them all
   unchecked unless you later want org/private-repo-specific plugins (each plugin's own docs
   list exactly which scope it needs).
2. On `github.com/abdullahtariqlatif/abdullahtariqlatif` -> Settings -> Secrets and variables ->
   Actions -> New repository secret. Name it exactly `METRICS_TOKEN`, paste the token value.
3. Push (or re-run) - it also triggers on push to `main`, so pushing this workflow file will
   try to run it immediately, but it'll fail until the secret exists. Re-run it manually
   (Actions tab -> "Metrics" -> Run workflow) once the secret is set.
Commits `github-metrics.svg` straight to `main` by default - that's what the README embeds.

## Keeping it in sync later

If your tech stack, employers' public framing, or pinned-worthy repos change, the source of
truth for what's safe to say publicly is still the Career Archive's
`03-application-materials/general-summary.md` and `01-source-of-truth/skills.md` - update this
README from there, the same displayed-vs-internal discipline used everywhere else in that
system (e.g. MERN shown unqualified here, same as the resume, even though `skills.md` still
tracks it internally at `learning` tier).
