# VFXEditor Taiwan API 13 maintenance

## Lineage

- Upstream: <https://github.com/0ceal0t/Dalamud-VFXEditor> (remote `origin`, default branch `main`).
- Base: `c320f08c981f3bf7353157c3d2faebcb00cba511` ("small tweaks", committed 2025-09-26). No tag. At this commit `VFXEditor/VFXEditor.csproj` uses `Dalamud.NET.Sdk/13.0.0` and `AssemblyVersion` 1.9.2.8; `VFXEditor/VFXEditor.json` sets `DalamudApiLevel` 13.
- Evidence: the next upstream commit moving `Dalamud.NET.Sdk` is `fbbfedb4c639da43a3f0f50863104f38121f5735` ("initial 7.4 changes (still need to fix selectors)", 2025-12-26), whose first parent is the base. It moves the SDK from `13.0.0` to `14.0.1` and `DalamudApiLevel` from 13 to 14.
- Submodule: `Penumbra.String` pinned at `c8611a0c546b6b2ec29214ab319fc2c38fe74793`, unmodified.
- Other remotes compared: `tw` (<https://github.com/cycleapple/Dalamud-VFXEditor-API13>), a Traditional Chinese fork. Its default branch `api13-zh-tw` (`e33fe0026779c413c6e20fbc76a94335f3f79d36`) branches from upstream at this same base (`git merge-base tw/api13-zh-tw origin/main` is `c320f08c`) and adds two commits: `004cdd9d` ("Add API 13 Traditional Chinese VFXEditor 1.9.2.9", localization across 222 files) and `e33fe002` ("Fix TC resource manager signature on API 13 (1.9.2.10)"). `api13` was created from `c320f08c`, not from `tw`, and contains neither commit. Only the `Constants.cs` change below matches `tw`.
- Local commits on `api13`: none.

## Changes

- `VFXEditor/Interop/Constants.cs` (staged, uncommitted): replaces `GetFileManager2Sig`, which matched a call site (`E8 ?? ?? ?? ?? 48 8B 1D ...`), with a pattern for the file-manager singleton accessor itself. Why, per the added comment: the caller's instructions differ on the Taiwan client. The new pattern is the one upstream adopted in `fbbfedb4`, and the hunk is identical to `tw` commit `e33fe002` (resulting blob `5fb670ad` in both).
- `VFXEditor/Formats/ScdFormat/ScdFile.cs` (unstaged): wraps `ScdFile.Import` in a try/catch that logs `Could not import <path> (.ogg must be Vorbis, not Opus)` through `Dalamud.Error`. Why, per the added comment: `Import` is `async void`, so an escaping exception kills the game process. Not specific to API 13 or the Taiwan client; neither upstream `main` nor `tw/api13-zh-tw` has this guard.

## Build and package

Build command: Not recorded.

Last verified build: Not recorded.

Deployed copy (manifest file dated 2026-10-25):

- `DalamudApiLevel`: 13.
- `AssemblyVersion`: 1.9.2.8, the base's version (`tw/api13-zh-tw` is 1.9.2.10).
- Whether it was built with the two changes above: Not recorded.

## Runtime verification

Confirmed in game: Not recorded.

Pending in the Taiwan client:

- Plugin load, with `GetFileManager2Sig` resolving.
- Importing an Opus `.ogg` into an `.scd` logs an error instead of crashing the game.

## Rebase policy

The base is the first parent of the API 14 transition `fbbfedb4`, so no newer upstream `main` revision stays on API 13. Take a later upstream fix only as an individual cherry-pick that keeps `Dalamud.NET.Sdk/13.0.0` and `DalamudApiLevel` 13. Before porting a Taiwan fix, compare `tw/api13-zh-tw`, which shares the base. Carry the changes above across any rebuild of the branch. Before replacing or rebasing, list local commits with `git log --oneline HEAD --not --remotes`.
