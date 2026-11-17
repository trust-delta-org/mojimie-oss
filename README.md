# mojimie-oss

Source code and build scripts for the open-source library that **Mojimie** (a comic reader for iPhone that reads ZIP / CBZ files on your device and NAS without unzipping) ships.
The library is licensed under the **GNU Lesser General Public License, version 2.1 or later (LGPL-2.1-or-later)**.
It is included **unmodified**. This repository lets you rebuild it and swap it for your own build.

> 日本語: アプリ「Mojimie」が配っている LGPL のライブラリ（libsmb2）の、ソースと焼き方の置き場です。
> 手を入れていません。ここのスクリプトで焼き直し、アプリの中のものと差し替えられます。
> 作り手: TDアプリ開発室 (Trust Delta Apps) ・ support@trust-delta.dev

## What is included

| Library | Version | Used on | Upstream | Pinned by |
|---|---|---|---|---|
| libsmb2 (the `lib` and `include` directories) | libsmb2-6.3 | iOS | https://github.com/sahlberg/libsmb2 | git commit `2bcb014b23a8c8cccf534eea8bda2fe5df453cfd` |

- `sources/` holds the exact source archive the app was built from. `SHA256SUMS` lists its checksum.
  - `libsmb2-libsmb2-6.3.tar.gz` is `git archive` of the commit above.
- `tools/` holds the build scripts the app is built with, copied verbatim. The comments in them are in Japanese.
- `LICENSES/` holds the license texts that ship with the library.
- libsmb2 is built with Kerberos and GSSAPI turned off (`-DENABLE_LIBKRB5=OFF -DENABLE_GSSAPI=OFF`). Its examples are not built.

## How the library is shipped

- **iOS:** as a dynamic framework inside the app bundle: `Mojimie.app/Frameworks/smb2.framework`.
  It holds a dynamic library (`Mach-O 64-bit dynamically linked shared library arm64`).

## Rebuilding (needs macOS with Xcode)

```sh
bash tools/build_libsmb2_apple.sh    # → ios/MojimieNet/Frameworks/smb2.xcframework
```

- The script fetches the source itself with `git clone` (`tools/fetch_libsmb2.sh`) and stops if the tag does not point at the pinned commit.
  The tree is the same as `libsmb2-libsmb2-6.3.tar.gz`.
- It builds three slices (iOS device arm64, iOS simulator arm64, macOS arm64; iOS deployment target 17.0) and wraps the library as a framework.
  The install name is `@rpath/smb2.framework/smb2`.
  Wrapping changes only the install name, `Info.plist` and the ad-hoc signature. The library code is not changed.

## Replacing the library in the app

1. Build your own version of the framework, with the same name and a compatible ABI (same major version).
2. Copy its binary over `Mojimie.app/Frameworks/smb2.framework/smb2`.
3. Re-sign the app with your own signing identity (`codesign --force --deep --sign <identity> Mojimie.app`) and install it on your device.

## License

- libsmb2: LGPL-2.1-or-later for the library. See `LICENSES/libsmb2-COPYING` and `LICENSES/libsmb2-LICENCE-LGPL-2.1.txt`.
  Its examples are BSD-licensed and are not built.
- The build scripts in `tools/` are released under the same license as the library they build (LGPL-2.1-or-later).
