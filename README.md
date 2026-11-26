# Ambero website

Static download page + privacy policy. No build step, no dependencies — plain HTML
with inline CSS/JS, Arabic first with an English toggle.

```
ambero_website/
  index.html          landing page (all download links live here)
  privacy/index.html  privacy policy (same file the app links to)
  assets/             icon + screenshots
```

## Publish (Cloudflare Pages, free for private repos)

1. Cloudflare dashboard → Workers & Pages → Create → Pages → Connect to Git → this repo.
2. Build command: **none**. Output directory: **ambero_website**.
3. Deployed URL: `https://<project>.pages.dev` → `/` is the landing page, `/privacy/` the policy.

GitHub Pages is not an option here: the repo is private and Pages for private repos is paid.

## Installers (Cloudflare R2, free tier)

Cloudflare Pages caps single files at 25 MB, so the installers live in an R2 bucket and
the page links to them. Upload with **stable names that you overwrite on every release**:

| File | Bucket key | Built with |
|---|---|---|
| Android APK | `android/ambero-latest.apk` | `flutter build apk --release --obfuscate --split-debug-info=build/symbols/android` |
| Windows ZIP | `windows/ambero-latest.zip` | Release folder, after deleting `data/flutter_assets/kernel_blob.bin` |

Then in `index.html`:

- Android card: replace `<span class="btn soon">…APK…</span>` with
  `<a class="btn ghost" href="https://<r2-domain>/android/ambero-latest.apk">…same label…</a>`.
- Windows card: same for `<r2-domain>/windows/ambero-latest.zip`.
- Delete the `soon` spans only when their URL works; a `soon` span is deliberately
  not a link so the page never ships a dead download.

Play Store link is live and needs no edit:
`https://play.google.com/store/apps/details?id=com.ambero.iptv`

## When the iOS build is approved

In both the Arabic and English cards, replace
`<span class="btn soon">قريبًا على App Store</span>` / `…Coming to the App Store…`
with `<a class="btn primary" href="https://apps.apple.com/app/idXXXXXXXX">…</a>`.

## Version strings

`1.0.1` is hard-coded in both cards (Arabic and English). Bump it together with
`pubspec.yaml` and the Settings screen.

## Privacy URL

After the first deploy, set the policy URL in the app build and in Play Console:

```
flutter build appbundle --release ... --dart-define=PRIVACY_URL=https://<project>.pages.dev/privacy/
```
