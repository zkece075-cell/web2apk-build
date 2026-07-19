# Build Runner (GitHub Actions)

Repo ini **bukan** tempat naruh source code project Flutter/Android kamu.
Isinya cuma "robot build" — 3 workflow yang dipicu (trigger) otomatis oleh bot Telegram
lewat GitHub API (`workflow_dispatch`).

```
.github/
└── workflows/
    ├── flutter-build.yml     # build project Flutter (assemble APK)
    ├── gradle-build.yml      # build project Android Studio/Gradle native
    └── web2apk-build.yml     # build APK WebView dari sebuah URL website
```

## Cara kerja singkat

1. User kirim `.zip` project ke bot Telegram.
2. Bot upload zip itu ke **GitHub Release** repo ini, dapat URL publiknya.
3. Bot trigger salah satu workflow di atas lewat GitHub API, kasih URL zip sebagai input.
4. Workflow download zip itu, extract, jalankan build (`flutter build apk` atau
   `./gradlew assemble...`), lalu upload hasil APK sebagai **artifact**.
5. Bot polling status run, download artifact, kirim APK-nya balik ke user di Telegram.

## Yang perlu kamu siapkan di sisi repo ini

- **Flutter & Gradle**: tidak perlu file tambahan apa pun — workflow-nya sudah
  lengkap, tinggal push folder `.github/workflows/` ini ke repo kamu.
- **Web2Apk**: workflow ini butuh **project template Android WebView** sudah ada
  di repo (folder `app/` dengan `build.gradle`, `gradlew`, dan Activity yang
  load URL dari `gradle.properties`). Kalau belum ada, opsi Web2Apk di bot tidak
  akan berfungsi sampai template-nya kamu commit ke sini.

## Setting yang wajib di bot (bukan di repo ini)

Bot (folder terpisah, `bot_build_gradle.zip`) butuh env var:

```
GITHUB_TOKEN=<PAT dengan scope repo + workflow>
GITHUB_OWNER=<username/organisasi repo ini>
GITHUB_REPO=<nama repo ini>
```

## Cara push repo ini ke GitHub

```bash
cd github_repo
git init
git remote add origin https://github.com/<owner>/<repo>.git
git add .
git commit -m "init: workflow build runner"
git branch -M main
git push -u origin main
```
