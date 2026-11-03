# Cloudflare Quick Tunnel for Android (No Root)

This repository contains a pure-Rust QUIC client for Cloudflare Quick Tunnels, with JNI bindings and a custom DoH (DNS over HTTPS) resolver, designed to work on non-rooted Android devices.

## The Problem

The official `cloudflared` client (written in Go) does not work on Android without root. It attempts to resolve DNS SRV records via `[::1]:53`, but no service listens on that port on non-rooted devices. See [cloudflare/cloudflared#1229](https://github.com/cloudflare/cloudflared/issues/1229).

## The Solution

This project bypasses the Go binary entirely and uses a pure-Rust QUIC client (`cloudflare-quick-tunnel-rs`). It includes:
1. **DoH Resolver:** Custom `hickory-resolver` to fetch DNS records over port 443.
2. **JNI Bindings:** C wrapper and Rust FFI to call the QUIC client from Kotlin/Flutter.
3. **Webpki-roots:** Bundled Mozilla root CAs, since Android does not ship `/etc/ssl/certs/ca-certificates.crt`.

## Structure

- `rust/cloudflare-quick-tunnel-rs/`: The forked Rust QUIC client.
- `android/app/src/main/cpp/qttunnel_jni.c`: The JNI wrapper.
- `android/app/src/main/kotlin/com/aitechdb/ghostchat/MainActivity.kt`: Example Kotlin code that loads the native library.

## Reference Implementation

This code is used in [GhostChat](https://github.com/ai-tech-db/ghostchat), an E2EE ephemeral chat app.

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later). See the LICENSE file for details.

The Rust library is a fork of `cloudflare-quick-tunnel-rs` (MIT OR Apache-2.0). See the NOTICE file for attribution.
