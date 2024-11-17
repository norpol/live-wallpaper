# live-wallpaper

High-Resolution Live Wallpapers

# Supported Sources

* [foto-webcam.eu](https://foto-webcam.eu) mostly Germany, Switzerland and Austria


* Defaults
  * `WEBCAM=simonyhuette`
    * Go to [foto-webcam.eu](https://foto-webcam.eu) for a list of webcams (webcam-name is in the URL)
  * `SWWW_TRANSITION=none`
    * check `swww img --help | grep env:` for supported variables
  * `$XDG_PICTURES_DIR/wallpapers` or `$HOME/Pictures/wallpapers`
  * `"/run/user/$UID/live-wallpaper-latest.jpg"`

# Goals

* Be resource friendly with service-providers
* Provide recent wallpapers

# Dependencies

* `Bash`
* `curl`
* `procps`: `pgrep`
* `gnugrep`
* `swww`
* `swayidle`
* `findutils`
* `swaymsg`
* `coreutils` `shuf`

## Dev

* `shfmt -w live-wallpaper`
* `shellcheck live-wallpaper`

# Setup Guides

## swaywm

In your `"${XDG_CONFIG:-${HOME}/.config}/sway/config"` add something like this

```i3config
# Background image daemon
exec swww-daemon
# Wrap in systemd user service for easier process management
exec_always systemd-run --property "Wants=graphical-session.target" \
              --property "BindsTo=graphical-session.target" --slice="$SWAY_SLICE" \
              --property Restart=on-failure -u live-wallpaper \
              --user -E "PATH" live-wallpaper || \
                systemctl --user restart live-wallpaper
# Pause updating wallpapers on idle
exec swayidle -w timeout 1800 "systemctl --user kill -s STOP live-wallpaper.service" \
     resume "systemctl --user kill -s CONT live-wallpaper.service"
```
