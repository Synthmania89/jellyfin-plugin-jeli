# JELI Release Checklist

Follow these steps every time you publish a new version.

## 1. Build the Release DLL

```powershell
cd C:\path\to\Jeli
dotnet build -c Release
# DLL is at: bin\Release\net9.0\Jellyfin.Plugin.Jeli.dll
```

## 2. Create the Release Zip

The zip must contain exactly 3 files (no subfolder):
```
Jellyfin.Plugin.Jeli.dll   ← from bin\Release\net9.0\
meta.json                   ← from project root (update version first!)
thumb.jpg                   ← from Web\thumb.jpg
```

Name the zip: `jellyfin-plugin-jeli_v1.1.0.1.zip` (match the version)

## 3. Get the MD5 Checksum

Run in PowerShell:
```powershell
(Get-FileHash "jellyfin-plugin-jeli_v1.1.0.1.zip" -Algorithm MD5).Hash.ToLower()
```

Or on Linux/Mac:
```bash
md5sum jellyfin-plugin-jeli_v1.1.0.1.zip
```

Copy the resulting hash — you'll need it for manifest.json.

## 4. Update manifest.json

Edit `manifest.json` in this repo and add a new entry at the TOP of the `versions` array:

```json
{
  "version": "1.1.0.1",
  "changelog": "Describe what changed in this version",
  "targetAbi": "10.11.11.0",
  "sourceUrl": "https://github.com/Synthmania/jellyfin-plugin-jeli/releases/download/v1.1.0.1/jellyfin-plugin-jeli_v1.1.0.1.zip",
  "checksum": "PASTE_MD5_HERE",
  "timestamp": "2026-06-10T00:00:00Z"
}
```

Keep older version entries below it (Jellyfin shows them for users on older server versions).

## 5. Create the GitHub Release

1. Go to your repo → **Releases** → **Draft a new release**
2. Tag: `v1.1.0.1` (must match sourceUrl exactly)
3. Title: `JELI v1.1.0.1`
4. Upload the zip file as a release asset
5. Paste the changelog in the description
6. Publish the release

## 6. Commit manifest.json

```bash
git add manifest.json
git commit -m "Release v1.1.0.1"
git push
```

Jellyfin users with your repo added will now see the update automatically.

---

## How Users Install

Tell users to add this URL in Jellyfin → Dashboard → Plugins → Repositories:

```
https://raw.githubusercontent.com/Synthmania/jellyfin-plugin-jeli/main/manifest.json
```

That's it. Jellyfin fetches the manifest, shows JELI in the catalog, downloads the zip from the release,
extracts the DLL, and on restart the plugin is active.
