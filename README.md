# iDared 32bit v1.5.0 — unsigned IPA builder

This is a **build recipe, not a precompiled IPA**. It compiles the **official `v1.5.0` tag** of <https://github.com/iDared32bit-emu/iDared32bit> and its Git submodules using an Xcode 27 GitHub Actions macOS runner, and creates an unsigned iOS ARM64 IPA for subsequent signing/sideloading.

Source release: <https://github.com/iDared32bit-emu/iDared32bit/releases/tag/v1.5.0> (reported commit starts `aeb508b`). This recipe does not build the constantly changing default branch. It uses an actual Git checkout rather than the supplied GitHub source archives because those archives omit the SDL and stb Git submodule content.

## Easiest setup in a web browser

1. Create a new **empty** repository in your GitHub account. A public repository lets eligible GitHub-hosted Actions macOS jobs use the public-repository minutes policy; a private repository uses the private-repository quota. A repository containing only this workflow contains no original app source or assets.
2. Open your new repository → **Add file → Create new file**. Set the filename to `.github/workflows/build-ipa.yml` and paste the entire contents of the included `build-ipa.yml`, then commit to the default branch.
3. Open **Actions → Build iDared 32bit v1.5.0 (unsigned IPA) → Run workflow**.
4. When GitHub shows the completed run, select its artifact **iDared32bit-v1.5.0-unsigned-ipa**. Extract the artifact ZIP to obtain `iDared32bit-v1.5.0-unsigned.ipa` and `SHA256SUMS.txt`.
5. **The IPA is unsigned.** Re-sign and install it with an appropriate iOS signing/sideloading solution. It cannot be installed directly without signing. For ordinary sideloading tools such as SideStore/AltStore/Sideloadly, compatible entitlements and a valid provisioning profile are needed; tool/device limits apply.

## Windows one-command setup (optional)

If Git and GitHub CLI (`gh`) are installed, you can execute `setup-windows.ps1` from PowerShell. The script prompts for GitHub sign-in when needed, creates a **new** GitHub repository containing only this workflow, and requests one build. It does not modify existing repositories. Use `-Public` to create a public repo instead of a private one.

```powershell
powershell -ExecutionPolicy Bypass -File .\setup-windows.ps1 -RepoName iDared32bit-IPA-Builder -Public
```

If GitHub has not yet indexed the new workflow when the script first tries to dispatch it, open your new repo's Actions page and use **Run workflow** manually.

## Alternative: run on your own Mac

You can checkout the same official source with `git clone --recursive --branch v1.5.0 https://github.com/iDared32bit-emu/iDared32bit.git` and open its `iphone/iDared32bit.xcodeproj` in Xcode. The workflow is just an automated equivalent, with code signing switched off.

## Build limits

* This package is **not a compiled or tested IPA**; without macOS/Xcode, the iOS build and launch cannot be validated here. The workflow contains structural verification of the arm64 app and archive packaging, which will run on GitHub.
* The source targets **iOS 16 or later**. The emulator's current game compatibility scope primarily covers iPhone OS 2.x–4.x, not arbitrary 32-bit games.
* The original Xcode app requests increased memory and extended virtual addressing entitlements. The unsigned build deliberately does not request them because a typical personal signing profile cannot grant every restricted entitlement. This may reduce compatibility with some titles.
* Do not upload or redistribute a renamed fork using the original proprietary iDared 32bit name, logo or app icon. Review the upstream README's **Name, logo and app icon** section before publicly distributing compiled binaries. This recipe is intended for a personal/private build.

Upstream source license: Mozilla Public License 2.0, with additional bundled component licenses and reserved branding rights in the source README.

### BuildKit fix (2026-11-02)

Windows PowerShell 5.1 sometimes treats the expected `gh repo view` 404 stderr as a terminating error. The setup script now checks repository presence without aborting, and also tolerates initial workflow indexing delays.
