# cloudflare-quick-tunnel-rs

A pure-Rust implementation of a Cloudflare Quick Tunnel client, designed specifically to work on Android without root.

This library is a fork of shadow3aaa/cloudflare-quick-tunnel-rs, modified to bypass the limitations of the official Go client on Android.

## The Problem

The official cloudflared client (written in Go) does not work on Android without root. It attempts to resolve DNS SRV records via [::1]:53, but no service listens on that port on non-rooted devices. See cloudflare/cloudflared#1229.

This library solves the problem by replacing the Go binary with a pure-Rust QUIC client.

## The Solution

To make Quick Tunnels work on non-rooted Android, this library implements three specific fixes:

1. DoH Resolver (DNS over HTTPS): Uses hickory-resolver to fetch DNS records over port 443, bypassing the carrier/OEM DNS filtering and the [::1]:53 requirement.
2. JNI Bindings: Includes C wrappers and Rust FFI bindings (in src/ffi.rs) to allow calling the QUIC client directly from Kotlin or Flutter.
3. Webpki-roots: Bundles Mozilla's root CA certificates directly into the binary (cf-edge-roots.pem), because Android does not ship with /etc/ssl/certs/ca-certificates.crt (which the Rust TLS stack expects by default).

## Building for Android

This library is designed to be compiled as a shared object (.so) for Android.

### Prerequisites

- Rust toolchain (rustup).
- Android NDK (set ANDROID_NDK_HOME).
- cargo-ndk.

### Compilation

    # Add the Android target
    rustup target add aarch64-linux-android

    # Install cargo-ndk
    cargo install cargo-ndk

    # Build the library
    export ANDROID_NDK_HOME="/path/to/your/ndk"
    cargo ndk build --release --target aarch64-linux-android

The resulting .so will be in target/aarch64-linux-android/release/.

## Project Structure

- src/api.rs: The main API for starting and stopping the tunnel.
- src/dns_adapter.rs: The custom DoH resolver.
- src/ffi.rs: The FFI layer exposed to the JNI wrapper.
- src/quic_dial.rs: The QUIC connection logic.
- cf-edge-roots.pem: Bundled Cloudflare edge certificates.

## Usage Example (Rust)

    use dl_cloudflare_quick_tunnel::QuickTunnel;

    // Start a tunnel on port 8080
    let mut tunnel = QuickTunnel::start(8080)?;
    let url = tunnel.url();
    println!("Tunnel available at: {}", url);

    // ... use the tunnel ...

    // Stop the tunnel
    tunnel.stop();

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later). See the LICENSE file for details.

### Third-party components

- This library is a fork of shadow3aaa/cloudflare-quick-tunnel-rs, originally licensed under MIT OR Apache-2.0.
- See the NOTICE file for full attribution details.

## Reference Implementation

This code is used in GhostChat (https://github.com/ai-tech-db/ghostchat), an E2EE ephemeral chat app.
