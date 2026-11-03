# Cloudflare Quick Tunnel for Android (No Root)

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Rust](https://img.shields.io/badge/Rust-1.86-000000?logo=rust)](https://www.rust-lang.org)
[![Platform](https://img.shields.io/badge/Platform-Android-green.svg)](https://www.android.com)
[![No Root](https://img.shields.io/badge/Requires_Root-No-red.svg)]()
[![Sponsor](https://img.shields.io/badge/Sponsor-ai--tech--db-pink?logo=github-sponsors)](https://github.com/sponsors/ai-tech-db)

This repository contains a pure-Rust QUIC client for Cloudflare Quick Tunnels, with JNI bindings and a custom DoH (DNS over HTTPS) resolver, designed to work on non-rooted Android devices.

## The Problem

The official `cloudflared` client (written in Go) does not work on Android without root. It attempts to resolve DNS SRV records via `[::1]:53`, but no service listens on that port on non-rooted devices. See [cloudflare/cloudflared#1229](https://github.com/cloudflare/cloudflared/issues/1229).

## The Solution

This project bypasses the Go binary entirely and uses a pure-Rust QUIC client (`cloudflare-quick-tunnel-rs`). It includes:
1. DoH Resolver: Custom `hickory-resolver` to fetch DNS records over port 443.
2. JNI Bindings: C wrapper and Rust FFI to call the QUIC client from Kotlin/Flutter.
3. Webpki-roots: Bundled Mozilla root CAs, since Android does not ship `/etc/ssl/certs/ca-certificates.crt`.

## Structure

- `rust/cloudflare-quick-tunnel-rs/`: The forked Rust QUIC client.
- `android/app/src/main/cpp/qttunnel_jni.c`: The JNI wrapper.
- `android/app/src/main/kotlin/com/aitechdb/ghostchat/MainActivity.kt`: Example Kotlin code that loads the native library.

## Reference Implementation

This code is used in [GhostChat](https://github.com/ai-tech-db/ghostchat), an E2EE ephemeral chat app.

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later). See the LICENSE file for details.

The Rust library is a fork of `cloudflare-quick-tunnel-rs` (MIT OR Apache-2.0). See the NOTICE file for attribution.

## Support & Contributing

This project is maintained by AI-Tech-DB. If you find it useful, here are some ways to contribute:

- Star the repository: It helps other developers discover the project.
- Report bugs: Open an issue with a clear description and logs.
- Submit pull requests: Improvements to the DoH resolver, JNI bindings, or documentation are welcome.
- Spread the word: Share the repository with Android developers who might need it.

If you find this project useful, consider sponsoring its development on GitHub.

---

Made with ❤️ by AI-Tech-DB
