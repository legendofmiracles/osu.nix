# osu.nix

[osu!](https://osu.ppy.sh)-related stuff for Nix and NixOS

## Wine

Wine 6.7 with low-latency audio patches applied. It currently provides the
lowest latency osu! environment on Linux, only limited by your hardware.

Test it in a shell:
```
$ nix shell github:fufexan/osu.nix
$ WINEPREFIX="path/to/osu/install" wine path/to/osu.exe
```
or add it to your `home.packages` or `environment.systemPackages` with
`inputs.osu-nix` after adding it as an input:
```nix
# flake.nix
{
  inputs.osu-nix.url = github:fufexan/osu.nix
  ...
}
```

Also available as an overlay if you prefer that.

**NOTE**: This wine version is available on
[Cachix](https://app.cachix.org/cache/osu-nix). Add it to your binary caches
to avoid building wine.

## PipeWire low latency

PipeWire is a new audio backend that replaces ALSA, PulseAudio and Jack. It
achieves lower latency than possible previously with Pulse, for lower CPU
overhead.

This module extends the PipeWire module from nixpkgs and makes it easy to
enable the low latency settings from a single line (or more).

Add it as a module to your configuration and enable it along with PipeWire:
```nix
{
  services.pipewire = {
    enable = true;
    # alsa is optional
    alsa.enable = true;
    alsa.support32Bit = true;
    # needed for osu
    pulse.enable = true;
    lowLatency = {
      enable = true;
      # defaults (no need to be set unless modified)
      quantum = 32;
      rate = 48000;
    };
  };
  
  # make pipewire realtime-capable
  security.rtkit.enable = true;
}
```

If you get no sound, you may want to increase `quantum`, usually to a power of
2 or the prefix of the `rate` (`48/48000` is exactly 1ms).

## Credits & Resources
 
Thank you
- [gonX](https://github.com/gonX) for providing the
[pulse patch](https://drive.google.com/drive/folders/17MVlyXixv7uS3JW4B-H8oS4qgLn7eBw5)
- [openglfreak](https://github.com/openglfreak) for provivding the
[secur32 patch](https://github.com/openglfreak/wine-tkg-userpatches/blob/next/patches/0010-crypto/ps0004-secur32-Fix-crash-from-invalid-context-in-InitializeSecurityConte.patch)
- [yusdacra](https://github.com/yusdacra) for helping me debug this flake

Find more info here
- [Flakes Wiki](https://nixos.wiki/wiki/Flakes)
