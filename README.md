# netsathi-releases

NetSathi's Android app releases: signed APKs, and one manifest per app and channel. No source code is kept here.

## The apps

| App | Channel | Package |
|---|---|---|
| The organisation app, for an organisation's staff | prod | `np.netsathi.app` |
| | beta | `np.netsathi.app.beta` |
| The customer app, for an organisation's customers | prod | `np.netsathi.customer` |
| | beta | `np.netsathi.customer.beta` |

A beta app is a separate app on the phone. It installs beside the prod one and does not replace it.

## Installing

1. Open the [Releases](https://github.com/Nepankaj/netsathi-releases/releases) page and pick a release.
2. Download the app's APK from that release's assets. Each file's name says its app, channel, version and build,
   for example `organisation-prod-0.2.0-12.apk`. No GitHub account is needed.
3. Open the downloaded file on the phone. Android asks you to allow installing apps from that source (your
   browser or file manager) the first time, then asks you to confirm the install.

Once installed, the apps check this repository's manifests themselves and offer their own updates.

## Signed and checked

- **Signed.** Each app and channel is signed with a key of its own. Android installs an update only when it is
  signed with the same key as the app already on the phone.
- **Checked.** Every APK's SHA-256 is in its manifest. The release pipeline downloads each APK again after
  publishing it and compares the SHA-256 before it writes the manifest. An app that downloads an update refuses
  a file whose SHA-256 differs, and never opens it.

To check a download yourself, compare its SHA-256 with the manifest's `sha256`. In PowerShell:
`Get-FileHash <file>.apk -Algorithm SHA256`; on Linux or macOS: `sha256sum <file>.apk`.

## The manifests

There is one manifest per app and channel, at a fixed address:

    https://raw.githubusercontent.com/Nepankaj/netsathi-releases/main/manifests/<app>-<channel>.json

Here `<app>` is `organisation` or `customer`, and `<channel>` is `prod` or `beta`. Each manifest describes the
newest APK of its app and channel:

| Field | What it is |
|---|---|
| `version` | the version shown to a person, for example `"0.2.0"` |
| `build` | the APK's build number (Android's version code); a newer APK always has a higher one |
| `sha256` | the APK's SHA-256, 64 lowercase hex digits |
| `size` | the APK's size in bytes |
| `url` | where to download the APK: an asset of one of this repository's releases |
| `min_build` | the oldest build that may keep running without updating; `0` for none |
| `notes` | what changed, as text; `""` for none |
| `package` | the app's package name, from the table above |

The release pipeline writes the manifests. They are never edited by hand.
