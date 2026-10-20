[![crates.io](https://img.shields.io/crates/v/ramp-ffmpeg.svg)](https://crates.io/crates/ramp-ffmpeg)
[![docs.rs](https://docs.rs/ramp-ffmpeg/badge.svg)](https://docs.rs/ramp-ffmpeg/)

RAMP Stack's fork of [ffmpeg-next](https://github.com/zmwangx/rust-ffmpeg), which continues the
original [ffmpeg](https://crates.io/crates/ffmpeg) crate by [meh.](https://github.com/meh/rust-ffmpeg).
This release preserves the `ffmpeg-next 9.0.0` API and uses
[ramp-ffmpeg-sys](https://github.com/ramp-stack/ffmpeg-sys) for corrected Apple framework linking.

Default features include `build-portable`, which builds and statically links FFmpeg without an
installed FFmpeg library. To select only decoding, formats, and scaling:

```toml
[dependencies.ffmpeg-next]
package = "ramp-ffmpeg"
version = "9.0.2"
default-features = false
features = ["build-portable", "codec", "format", "software-scaling"]
```

The FFmpeg 9.0.2 source is bundled in `ramp-ffmpeg-sys`, so building does not download FFmpeg or require
Git. Building requires a native C compiler and Make; Apple targets use the corresponding Xcode
compiler and SDK with pregenerated Rust bindings. The executable does not require FFmpeg shared
libraries at runtime. `build-portable` uses baseline CPU settings instead of tuning the binary for
the build machine.

For Intel targets, `build-portable` also enables `bundled-nasm`, which builds NASM from bundled
source for the build host and preserves FFmpeg's assembly optimizations. ARM targets do not build
NASM, and no separate NASM installation is needed.

Enable `generate-bindings` when adding a target or regenerating bindings; this requires libclang.

FFmpeg retains its own license; the bundled configuration uses FFmpeg's LGPL components unless
additional licensing or external codec features are enabled. See [FFmpeg licensing](https://ffmpeg.org/legal.html).

To use an existing FFmpeg installation, set `default-features = false` and enable
`generate-bindings` plus the required components. This generates bindings for that installation's
ABI rather than using the bundled FFmpeg 9.0.2 bindings. See the
[upstream build notes](https://github.com/zmwangx/rust-ffmpeg/wiki/Notes-on-building).

Documentation:

- [docs.rs](https://docs.rs/ramp-ffmpeg/);
- [FFmpeg user manual](https://ffmpeg.org/ffmpeg-all.html);
- [FFmpeg Doxygen](https://ffmpeg.org/doxygen/trunk/).

*Note on upgrading to v4.3.4 or later: v4.3.4 introduced automatic FFmpeg version detection, obsoleting feature flags `ffmpeg4`, `ffmpeg41`, `ffmpeg42` and `ffmpeg43`. If you manually specify any of these features, now is the time to remove them; if you use `ffmpeg43` through the `default` feature, it's still on for backward-compatibility but it has turned into a no-op, and you don't need to do anything. Deprecation plan: `ffmpeg43` will be dropped from default features come 4.4, and all these features will be removed come 5.0.*

*See [CHANGELOG.md](CHANGELOG.md) for other information on version upgrades.*

A word on versioning: major and minor versions of this crate track major and minor versions of FFmpeg, e.g. 4.2.x of this crate has been updated to support the 4.2.x series of FFmpeg. Patch level is reserved for changes to this crate and does not track FFmpeg patch versions. Since we can only freely bump the patch level, versioning of this crate differs from semver: minor versions may behave like semver major versions and introduce backward-incompatible changes; patch versions may behave like semver minor versions and introduce new APIs. Please peg the version you use accordingly.
