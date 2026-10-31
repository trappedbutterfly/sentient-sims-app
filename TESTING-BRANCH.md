# The `jaz-testing` branch

For Claude, working with Kean, the Sentient Sims mod tester. Read all of this before you change
anything in Kean's folders, then explain it to Kean in plain words, one step at a time.

## The goal, in one paragraph

Kean plays The Sims with **this** app as her everyday Sentient Sims app, so Jaz's newest version
gets real play before it goes to Gus (the mod's author, who publishes the public app). The main thing
under test is the **lean voice pipeline** (below). Everything else on the list is a bonus to notice
while playing. Kean's own coding projects are separate and never happen on this branch (see "Kean's
own code").

## If you are reading this before setting anything up

You may be reading this with `git show origin/jaz-testing:TESTING-BRANCH.md` from Kean's usual
`sentient-sims-app` folder. Good: do not switch that folder's branch. Kean may have her own work in
it, and the setup below leaves it exactly as it is.

If her usual folder was already switched to `jaz-testing`, run `git status` there. If it shows
changes, those are Kean's own work that came along with the switch: tell her, then move them to her
own branch (see "If her work ended up in the wrong place") before going on.

## What this branch is

Gus's public app (`guspuffygit/sentient-sims-app`, `main`, version 4.6.11) plus one commit per update
from Jaz, each listing what changed. It is only the app: the game mod stays whatever Kean normally
plays with. Jaz's side owns the branch and pushes to it (JazKieten is a collaborator on Kean's fork for
that).

Things Jaz is still building or only uses on stream were left out on purpose. If a feature from Jaz's
streams is missing here, that is expected, not a bug. Do not go looking for it or try to rebuild it.

## Two folders: one to play, one to code

The app Kean runs is whatever branch is checked out in the folder she starts it from. To keep
"playing Jaz's version" and "my own coding" from mixing, use two folders:

| Folder | Branch | Used for |
|---|---|---|
| `sentient-sims-app` (her usual clone) | her own `kean/...` branches | her own coding |
| `sentient-sims-app-testing` (new, next to it) | `jaz-testing`, always | playing and testing |

The testing folder is a git **worktree**: a second folder of the same clone, so both see the same
branches, commits and stashes. Nobody ever edits files or commits in it.

## Setting it up

Do these with Kean, one at a time, and say what each step is for.

1. **Check the remotes.** In her usual folder, `git remote -v`. Below, `origin` means **Kean's
   fork** (`github.com/<her-username>/sentient-sims-app`). If `origin` is Gus's repo instead, use
   the remote that points at her fork, or add it: `git remote add fork <her fork's URL>`. Gus's repo
   should be a remote named `upstream`; add it if missing:
   `git remote add upstream https://github.com/guspuffygit/sentient-sims-app.git`.
   No clone yet: `git clone https://github.com/<her-username>/sentient-sims-app.git` makes her usual
   folder.
2. **Make the testing folder.** In her usual folder:
   ```
   git fetch origin
   git worktree add --track -b jaz-testing ../sentient-sims-app-testing origin/jaz-testing
   ```
   If git says a `jaz-testing` branch already exists, drop `--track -b jaz-testing` and give
   `jaz-testing` as the last word instead. If it says the branch is checked out in her usual folder,
   switch that folder back to her own branch first (after the `git status` check above).
3. **Node.** The app needs Node **24.15.0** (`.nvmrc`). With nvm: `nvm install 24.15.0` and
   `nvm use 24.15.0`. Check with `node -v`.
4. **Install**, in the testing folder: `npm ci`. A few minutes the first time.
5. **Back up her saves' AI data.** The app keeps one database per save in
   `Documents/Electronic Arts/The Sims 4/Mods/sentient-sims/` (the `.db` files; on a Windows PC with
   OneDrive it is the OneDrive Documents folder). Copy that folder somewhere safe. This build adds a
   few empty tables to a save's database on first load. The public app ignores them, so going back
   is safe, but a backup costs nothing.
6. **Run it**, from the testing folder. Close the installed Sentient Sims app first (two copies fight
   over the same ports). Then `npm run start`. Start the app **before** the game.
7. **Settings.** An app started with `npm run start` keeps its settings apart from the installed app
   (Windows `%APPDATA%\Electron\config.json`, Mac `~/Library/Application Support/Electron/config.json`),
   so Kean enters her AI provider and keys once in this app's Settings page, and logs in again if it
   asks. Every app she starts from either folder shares those settings.

To go back to the public app at any time: close this one and open the installed app as usual.

## What to test

### The lean voice pipeline (the main one)

On by default: Settings > Advanced > **Lean voice pipeline**. A conversation used to make four AI
calls per round (director briefing, actors, reviewer, scores); lean makes one call per Sim per round
and carries the game's reason for the conversation into every round. Lines should start in about
1.5 s instead of about 5, for about a third of the cost. The switch takes effect on the next
conversation, no restart.

While playing, notice:
- Does the first line come quickly once Sims start talking?
- Are the lines a natural length (not one word, not speeches)?
- Does a conversation stay on what the game started it as for all three rounds (a "talk about
  hobbies" stays about hobbies)?
- When a Sim tells a lie (for example a social where they lie about their career), do they keep it up
  through the whole conversation, without the other Sim somehow knowing?
- Talking to a Sim yourself (chat window or voice): a straight answer, and "<Name> has said their
  piece" when they are done.
- Now and then, switch it off for a while to feel the old pipeline, and say which reads better.

Note the AI provider and model in every report: lean behaves differently on different models.

### Also new compared with the public app

1. **Conversation moodlets.** When a voiced conversation ends, each Sim in it can get a Sentient
   moodlet: one of 15 emotions, +1 to +3, an hour to most of a day depending on what was said, with a
   sentence in the tooltip about why. Small talk leaves nothing. (The public app only does this after
   the typed chat window.) Does the emotion fit, does a big moment land stronger and longer than a
   light chat, does the tooltip text show?
2. **Stale conversations are dropped instead of aired late.** On a busy lot, a conversation the
   game has already moved past (over a minute old, or the Sims went off with someone else or left the
   lot) is skipped rather than played minutes later.
3. **ElevenLabs voices.** Each line is spoken in the speaker's mood, long replies are stitched
   smoothly, the next conversation is prepared ahead, and on the v4 model the neighbouring lines are
   sent as context so the delivery flows. Needs Kean's own ElevenLabs key.
4. **Name pronunciation.** A Sim's name can be given a pronunciation (IPA, in the Sims page), and
   every voiced line uses it, whoever says the name. Keep the syllable dots, for example `/ˈhæl.i/`.
5. **Word-by-word subtitles.** Voiced lines carry the time each word is spoken, so a mod that reads
   it can reveal words in time with the voice. With her current mod the line simply shows whole.
6. **Voice input with ElevenLabs Scribe.** Talking to Sims by voice can use Scribe v2, which is told
   the names of the Sims around, so names come out spelled right. Pick ElevenLabs as the provider in
   the voice input settings; it reuses the ElevenLabs key from the Voices settings.
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

## Messages to Jaz's Claude

Kean asks you first, and you answer what you can: the code in her checkout, setup, git, running the
app, her own projects. What only Jaz's side can answer goes to Jaz's Claude through a private GitHub
repo, **`JazKieten/kean-messages`** (only Jaz and Kean can see it). One issue per message; Jaz's
Claude answers on the issue and you read the reply yourself, so nobody copies messages around. Kean
and Jaz keep talking on Discord as usual. That repo's README has the details; read it once.

When Kean says **"send a message to Jaz's Claude"**, that is what she means. Also send one, after
telling her, when:
- something in `jaz-testing` looks broken and you checked it is not her setup;
- it needs Jaz's side to do something (a fix, a new build, access, a decision);
- the answer depends on Jaz's own build or plans, which you cannot see;
- Kean wants to change `jaz-testing` itself, or it is unclear whether her project belongs with Gus or
  Jaz;
- Kean has test feedback she wants Jaz to have (label `feedback`).

Write each issue so it stands on its own: what Kean was doing and expected, what happened (exact
error text and the `main.log` lines from around then, API keys removed), `git log -1 --oneline` in the
testing folder, Windows or Mac, the AI provider and model, what you already tried, and the question.
The app's log is `main.log` (Windows `%APPDATA%\Electron\logs\`, Mac `~/Library/Logs/Electron/`); the
Open Log Folder button on the Home page opens it.

Kean can still message Jaz directly any time, and should for anything urgent (the game or a save
looks broken).

## Getting Jaz's updates

When Jaz says there is a new build, in the testing folder:
```
git pull --ff-only
npm ci
```
Then restart the app. `npm ci` only matters when `package-lock.json` changed, but it is safe every
time. If `--ff-only` refuses, something was committed in the testing folder; see "If her work ended
up in the wrong place".

## Kean's own code

The rule: **code only in her usual folder, always on her own `kean/<short-name>` branch, never on
`jaz-testing`.** Make the branch before the first change, not after.

Where the branch starts depends on what the change is for. Ask her if it is not obvious.
- **Anything that could go to Gus** (a fix, a feature for every player, her own experiment): start
  from Gus's code.
  ```
  git fetch upstream
  git switch -c kean/<short-name> upstream/main
  ```
  Only a branch started this way can become a pull request to Gus. One started from `jaz-testing`
  would carry all of Jaz's unreleased changes with it.
- **A change to something that only exists in `jaz-testing`** (the lean pipeline, the moodlets):
  start from Jaz's branch, and it goes to Jaz, not Gus.
  ```
  git fetch origin
  git switch -c kean/<short-name> origin/jaz-testing
  ```

Commit as she goes and push the branch to her fork (`git push -u origin kean/<short-name>`), so it is
backed up and Jaz can see it. Tell Jaz the branch name when something is ready.

Running her own code: close the testing app, then `npm run start` from her usual folder. A branch
started from Gus's code is the public app plus her change, not Jaz's version; that is expected.
Switching back to playing means closing that app and starting the one in the testing folder.

### If her work ended up in the wrong place

- **Uncommitted changes in the testing folder** (or carried onto `jaz-testing` by a switch): in that
  folder `git stash push -u -m "<what it is>"`; in her usual folder make her `kean/` branch as above,
  then `git stash pop`. Stashes are shared between the two folders.
- **Commits on `jaz-testing`:** in that folder `git branch kean/<short-name>` (keeps them), check with
  `git log kean/<short-name>` that they are there, then, with Kean's OK, put `jaz-testing` back:
  `git reset --hard origin/jaz-testing`.

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
