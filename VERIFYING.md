# Verifying a SpamBlaster download

Every SpamBlaster release is built by an automated pipeline, signed, and published with enough
evidence for you to check that the file you downloaded is the file we built. Nothing here needs
an account, and none of it needs access to our private source repository.

There are three independent checks. Doing the first is enough for most people. The second and
third answer different questions and are worth doing if you care where a binary came from.

## 1. Check the hash

Download the assets into one directory, then:

```bash
sha256sum -c SHA256SUMS.txt
```

PowerShell has no `sha256sum`, so on Windows hash the file and compare it against the value
listed in `SHA256SUMS.txt` or on [spamblaster.org/download](https://spamblaster.org/download):

```powershell
Get-FileHash SpamBlaster_1.0.0_x64-setup.exe -Algorithm SHA256
```

## 2. Check the build provenance

This proves that a specific workflow, at a specific commit, produced these exact bytes. It is a
[SLSA](https://slsa.dev) build-provenance attestation, signed through Sigstore, and it covers
every file listed in `SHA256SUMS.txt`, including the bill of materials itself.

The check is **fully offline** and needs the `release-attestation.json` and `trusted_root.jsonl`
assets from the same release:

```bash
gh attestation verify SpamBlaster_1.0.0_x64-setup.exe \
  --bundle release-attestation.json \
  --custom-trusted-root trusted_root.jsonl \
  --repo wilburn-pacific-company/spam-blaster
```

Two things about that command are deliberate:

- It names `spam-blaster`, our **private build repository**, not the repository hosting the
  download. Attestations name the repository whose workflow produced the bytes. You do not need
  access to it to verify the signature.
- Do **not** use `--owner`. That form resolves the attestation through the GitHub API, which
  cannot work for anyone without read access to a private repository.

`trusted_root.jsonl` is published for airgapped use. If you are online, prefer generating your
own trust anchor with `gh attestation trusted-root`, because an independent anchor is better
than one taken from the same release you are checking.

## 3. Check the release itself

This repository publishes immutable releases, so GitHub independently attests the asset set.
This check runs through the normal API and needs nothing but the file:

```bash
gh attestation verify SpamBlaster_1.0.0_x64-setup.exe \
  --repo wilburn-pacific-company/spam-blaster-releases
```

Check 2 says "this workflow, at this commit, produced these bytes." Check 3 says "these bytes
were the assets of this release." Both should pass.

## What each file in a release is

| File | What it is |
|---|---|
| `SHA256SUMS.txt` | SHA-256 of every asset, in `shasum` format |
| `release-bom.json` | Bill of materials: source commit, per-artifact contents, signing identity, dependency mapping |
| `rust-core-daemon.cdx.json` | CycloneDX SBOM for the Rust daemon: the filter engine, the local store and the loopback API |
| `js-workspace.cdx.json` | CycloneDX SBOM for the JavaScript workspace: the dashboard, both Outlook panes, the Thunderbird add-on and shared packages |
| `dotnet-outlookclassic-core.cdx.json` | CycloneDX SBOM for the .NET extraction and preview logic used by the classic-Outlook add-in |
| `release-attestation.json` | The SLSA build-provenance attestation used in check 2 |
| `trusted_root.jsonl` | The Sigstore trusted root captured at release time, for offline verification |

## How SpamBlaster is signed

| Artifact | Signature |
|---|---|
| The Windows installer and the `SpamBlaster.exe` inside it | Authenticode, **Wilburn Pacific Company**, RFC-3161 timestamped, through Azure Artifact Signing |
| The macOS `.dmg` and the application inside it | Apple **Developer ID**, hardened runtime, notarized by Apple and stapled. Team `B8RQ33BAP8` |
| The Thunderbird `.xpi` | **Unsigned, and that is not an omission.** Thunderbird operates no add-on signing server and does not require signed add-ons ([Bugzilla 1549562](https://bugzilla.mozilla.org/show_bug.cgi?id=1549562), resolved WONTFIX). There is no signature to check at any level. Its integrity rests on the SHA-256 and the build attestation, both of which cover it. |

Signatures are verified inside the pipeline before anything is uploaded, and every published
asset is re-downloaded and re-hashed against `SHA256SUMS.txt` before the release goes public.

## First run

Windows SmartScreen may show "Windows protected your PC" for a new publisher. Click
**More info**, confirm the publisher reads **Wilburn Pacific Company**, then **Run anyway**.
SmartScreen reputation builds as more people download a signed application, so this notice
becomes less common over time. It is not a sign that anything is wrong with the file, and the
publisher name is the thing worth checking.

On macOS the installers are notarized by Apple and stapled, so Gatekeeper opens them normally.

## If a check fails

Do not run the file. Please open an issue at
[spam-blaster-issues](https://github.com/wilburn-pacific-company/spam-blaster-issues) with the
filename, the hash you got, and where you downloaded it from.
