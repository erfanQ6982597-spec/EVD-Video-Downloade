# Releasing EVD

The app targets `net8.0-windows`, has **zero NuGet package dependencies**, and
is published **self-contained** — end users never need to install the .NET
runtime separately. This is what makes a plain "download → install → run"
GitHub Release possible.

## 1. Publish the self-contained build

From the `ERFAN.VideoDownloader\` project folder, either:

```powershell
.\publish.ps1
```

or run the equivalent command directly:

```powershell
dotnet publish ERFAN.VideoDownloader.csproj -c Release -p:PublishProfile=win-x64-selfcontained
```

Output: `ERFAN.VideoDownloader\bin\Release\net8.0-windows\win-x64\publish\` —
a single self-contained `.exe` (the .NET 8 runtime is bundled inside it) plus
the `Tools\` folder next to it, if you placed `yt-dlp.exe` / `ffmpeg.exe` /
`ffprobe.exe` / `deno.exe` there beforehand (see `Tools\README.txt`).

This does **not** change normal `dotnet build` / F5 debugging — those still
produce the same framework-dependent output as before. The self-contained,
single-file settings only apply when this specific publish profile is used.

## 2. Build the installer

With [Inno Setup](https://jrsoftware.org/isinfo.php) installed:

```powershell
iscc installer\ERFAN.VideoDownloader.iss
```

This packages the publish output above into
`installer\Output\EVD-Setup.exe`.

## 3. Publish to GitHub Releases

Upload `EVD-Setup.exe` as a release asset. A user who
downloads and runs it gets a working app immediately — no manual .NET
install, no manual yt-dlp/ffmpeg path configuration (as long as `Tools\` was
populated before step 1).

## Notes

- Bump `MyAppVersion` in `installer\ERFAN.VideoDownloader.iss` (and the
  assembly version in the `.csproj`, if you version that too) before each
  release.
- `yt-dlp.exe` and `ffmpeg.exe` are **not** committed to the repo (they're
  large and change often) — grab current builds per `Tools\README.txt` and
  drop them in `ERFAN.VideoDownloader\Tools\` before running `publish.ps1`.
