# IPTV Vision

Kullanıcının kendi yasal M3U/M3U8 veya Xtream Codes kaynaklarını ekleyerek içeriklerini cihazında yönetebildiği mobil IPTV oynatıcı.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm --filter @workspace/iptv-vision run dev` — run the Expo mobile preview
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Expo SDK 57, React Native, TypeScript
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/iptv-vision/app/` — Expo Router screens
- `artifacts/iptv-vision/context/AppContext.tsx` — local source, content, favorite, and history state
- `artifacts/iptv-vision/components/vision.tsx` — shared mobile UI primitives
- `artifacts/iptv-vision/constants/colors.ts` — dark visual theme
- `artifacts/iptv-vision/assets/images/icon.png` — generated app icon
- `artifacts/iptv-vision/app/player.tsx` — Expo Video playback surface
- `android-native/` — Kotlin + Jetpack Compose Android Studio project
- `android-native/app/src/main/java/com/iptvvision/player/data/` — Room, M3U/XMLTV parsers, Xtream and TMDB clients
- `android-native/app/src/main/java/com/iptvvision/player/ui/IptvVisionApp.kt` — native navigation, source, library, details, EPG, and Media3 player screens

## Architecture decisions

- The first build is local-first: sources, parsed items, favorites, and history use AsyncStorage and do not require an account or server database.
- User-provided M3U data is parsed on-device; only user-entered URLs are fetched.
- The player uses the Expo Video native-compatible surface so HLS and other supported stream formats can be played on supported devices.
- No bundled channels, credentials, or IPTV endpoints are included.
- The native Android implementation is kept separately from the Expo preview so the existing preview remains usable while Android Studio builds are verified.

## Product

- Dark mobile IPTV shell with Home, Live TV, Movies, Series, Favorites, History, and Settings sections.
- M3U/M3U8 URL and Xtream Codes source entry, refresh, delete, status, and content counts.
- M3U EXTINF parsing with channel name, group, logo, and basic movie/series classification.
- Local favorites/history and a full-screen playback route.
- Native Android build includes Media3, device M3U picker, XMLTV parsing, Xtream live/VOD/series endpoints, TMDB enrichment, IMDb ID matching, YouTube trailer links, and audio/subtitle track selection.

## User preferences

- Use Turkish user-facing copy.

## Gotchas

- Empty states are intentional until the user adds a legal source.
- Expo Go startup logs may report a missing `libglib-2.0.so.0` for React Native DevTools; Metro still starts and the app preview works.
- Native Gradle configuration was resolved successfully, but APK/unit-test execution is blocked in this workspace because Android SDK (`ANDROID_HOME`) is not installed.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
