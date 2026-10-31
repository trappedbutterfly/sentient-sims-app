# The `jaz-testing` branch

For Claude, working with Kean, the Sentient Sims mod tester. Read all of this before you touch the
repo, then explain it to Kean in plain words when she asks what the branch is or how to use it.

## What this branch is

`jaz-testing` is Jaz's newest version of the **Sentient Sims companion app**, cut down to what is
meant to reach every player eventually. Kean tests it before it goes to Gus (the mod's author, who
publishes the public app). It is only the app. The game mod stays whatever Kean normally plays with.

It is Gus's public app (`guspuffygit/sentient-sims-app`, `main`, version 4.6.11) plus one commit per
update from Jaz. Each commit message lists what changed. Jaz's side owns this branch and pushes to it.

Things Jaz is still building or only uses on stream were left out on purpose. If a feature from Jaz's
streams is missing here, that is expected, not a bug. Do not go looking for it or try to rebuild it.

## What is new compared with the public app

These are the things to test. Ask Kean which ones she wants to try first.

1. **Lean voice pipeline (on by default).** Conversations make one AI call per Sim per round
   instead of four stages, so lines start sooner and cost less. The switch is in Settings >
   Advanced > Directed Scenes ("lean" or "full"). Worth testing: do conversations still make sense,
   do Sims stay on topic for all three rounds, does a Sim who is lying keep the lie going.
2. **Stale conversations are dropped instead of aired late.** On a busy lot, a conversation that
   the game has already moved past (over a minute old, or the Sims have gone off with someone else
   or left the lot) is skipped rather than played minutes later.
3. **ElevenLabs voices.** Each line is spoken in the speaker's mood, long replies are stitched
   smoothly, the next scene is prepared ahead, and on the v4 model the neighbouring lines are sent
   as context so the delivery flows. Needs Kean's own ElevenLabs key.
4. **Name pronunciation.** A Sim's name can be given a pronunciation (IPA, in the Sims page), and
   every voiced line uses it, whoever says the name. Keep the syllable dots, for example `/ˈhæl.i/`.
5. **Word-by-word subtitles.** Voiced lines carry the time each word is spoken, so the subtitle or
   speech bubble can reveal words in time with the voice. This needs a mod build that reads it; with
   an older mod the line simply shows whole.
6. **Voice input with ElevenLabs Scribe.** Talking to Sims by voice can use Scribe v2, which is
   told the names of the Sims around, so names come out spelled right. Pick ElevenLabs as the
   provider in the voice input settings; it reuses the ElevenLabs key from the Voices settings.
7. **Plain-language errors.** Patreon link problems, the error popup, voice and AI status say what
   went wrong and what to do, instead of raw error text.
8. **Sleep diaries.** When the AI refuses to write a diary (it happens around WickedWhims nights on
   some models), it is written once more without that part instead of storing the refusal.
9. **Memories and narration.** Unmapped interactions read as a plain sentence ("Kean is paying
   bills.") instead of the raw game label.
10. **Facts.** A Sim knows their home and who they live with, by name.
11. **Lot descriptions.** Room-by-room details and lot traits in plain words, and the Locations
    editor shows which description the game is using right now.

Gus's debug pages (Chat, Traits, OfflineMemory, Log Viewer, Mapping Browser) are here as in the
public app; they show when debug mode is on.

## Setting it up on Kean's computer

Do these with Kean, one at a time, and say what each step is for.

1. **Give Jaz access to the fork.** On github.com, in Kean's fork of `sentient-sims-app`:
   Settings > Collaborators > Add people > `JazKieten`. Jaz accepts the invite from email. This lets
   Jaz push the branch; it does not give access to anything else Kean owns. Also press
   **Sync fork** on the fork's main page first, so the fork has Gus's latest (a push can be refused
   when the fork is behind Gus).
2. **Get the code.** In Kean's local clone of her fork:
   ```
   git fetch origin
   git switch jaz-testing
   ```
   If Kean has no clone yet: `git clone https://github.com/<kean-github-username>/sentient-sims-app.git`
   and then the two lines above inside it.
3. **Node.** The app needs Node **24.15.0** (`.nvmrc`). With nvm: `nvm install 24.15.0` and
   `nvm use 24.15.0`. Check with `node -v`.
4. **Install.** `npm ci` in the repo folder. It takes a few minutes the first time.
5. **Back up her saves' AI data first.** The app keeps one database per save in
   `Documents/Electronic Arts/The Sims 4/Mods/sentient-sims/` (the `.db` files; on a PC with
   OneDrive it is the OneDrive Documents folder). Copy that folder somewhere safe. This build adds a
   few empty tables to a save's database on first load. The public app ignores them, so going back
   is safe, but a backup costs nothing.
6. **Run it.** Close the installed Sentient Sims app first (two copies fight over the same ports).
   Then `npm run start`. Start the app **before** the game.
7. **Settings carry over.** The dev app keeps its settings separately from the installed app (on
   Windows in `%APPDATA%\Electron\config.json`), so Kean enters her AI provider and keys once in
   this app's Settings page.

To go back to the public app at any time: close this one and open the installed app as usual.

## Getting Jaz's updates

When Jaz says there is a new build:
```
git fetch origin
git switch jaz-testing
git merge --ff-only origin/jaz-testing
npm ci
```
Then restart the app. `npm ci` only matters when `package-lock.json` changed, but it is safe to run
every time. If `--ff-only` refuses, someone committed on `jaz-testing` locally; see the next part.

## Kean building on top of it

`jaz-testing` belongs to Jaz's side: never commit on it, or the next update will not apply cleanly.
For Kean's own changes:
```
git switch -c kean/<short-name> jaz-testing
# ...work, commit...
git push -u origin kean/<short-name>
```
To pick up a new Jaz build in that branch: `git fetch origin && git merge origin/jaz-testing`.
Tell Jaz the branch name when something is ready; Jaz's side brings it over from Kean's fork.

A pull request to **Gus** must start from Gus's `main`, not from `jaz-testing`, or it would carry all
of Jaz's unreleased changes with it: `git fetch upstream && git switch -c pr/<name> upstream/main`
(add the remote once: `git remote add upstream https://github.com/guspuffygit/sentient-sims-app.git`).

## Reporting what she finds

For each thing Kean tests, note: what she did, what she expected, what happened, and the time. The
app's log is `main.log` in the app's logs folder (Windows: `%APPDATA%\Electron\logs\`); the Open Log
Folder button on the Home page opens it. Send Jaz the log lines from around that time with the note.

## For Claude: working in this tree

- `CLAUDE.md` describes the codebase; it is Gus's, plus notes on the features above.
- Run tests with `npm run test:unit` (the plain `npm run test` also runs tests that need AWS and
  OpenAI credentials). `npm run compile` type-checks. `npm run lint` reports many existing problems
  across the whole tree; lint only the files you change (`npx eslint <files>`).
- The tree is the public ("core") build: `tiers.json` lists paths that only exist in other builds,
  and `src/main/sentient-sims/tiers/index.ts` is generated in its core form. Do not regenerate it in
  dev form, and do not add files listed in `tiers.json`.
- Do not change anything in Kean's game install, Mods folder or save databases without asking her
  first, and never close or restart her game yourself; ask her to.
