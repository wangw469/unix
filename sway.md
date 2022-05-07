sway(1)                         General Commands Manual                         sway(1)

NAME
       sway - An i3-compatible Wayland compositor

SYNOPSIS
       sway [options...] [command]

OPTIONS
       -h, --help
           Show help message and quit.

       -c, --config <config>
           Specifies a config file.

       -C, --validate
           Check the validity of the config file, then exit.

       -d, --debug
           Enables full logging, including debug information.

       -v, --version
           Show the version number and quit.

       -V, --verbose
           Enables more verbose logging.

       --get-socketpath
           Gets the IPC socket path and prints it, then exits.

DESCRIPTION
       sway was created to fill the need of an i3-like window manager for Wayland. The
       upstream i3 developers have no intention of porting i3 to Wayland, and projects
       proposed by others ended up as vaporware. Many thanks to the i3 folks for pro‐
       viding such a great piece of software, so good that your users would rather
       write an entirely new window manager from scratch that behaved exactly like i3
       rather than switch to something else.

       You can run sway directly from a tty, or via a Wayland-compatible login manager.

CONFIGURATION
       sway searches for a config file in the following locations, in this order:

       1.   ~/.sway/config
       2.   $XDG_CONFIG_HOME/sway/config (suggested location)
       3.   ~/.i3/config
       4.   $XDG_CONFIG_HOME/i3/config
       5.   /etc/sway/config
       6.   /etc/i3/config

       If unset, $XDG_CONFIG_HOME defaults to ~/.config.

       An error is raised when no config file is found. The recommended default config‐
       uration is usually installed to /etc/sway/config; you are encouraged to copy
       this to ~/.config/sway/config and edit it from there.

       For information on the config file format, see sway(5).

IPC COMMANDS
       Though swaymsg(1) is generally preferred, you may run sway command to send com‐
       mand to the running instance of sway. You can also issue commands with i3-msg(1)
       or even with i3(1).

ENVIRONMENT
       The following environment variables have an effect on sway:

       SWAYSOCK
           Specifies the path to the sway IPC socket.

       XKB_DEFAULT_RULES, XKB_DEFAULT_MODEL, XKB_DEFAULT_LAYOUT, XKB_DEFAULT_VARIANT,
       XKB_DEFAULT_OPTIONS
           Configures the xkb keyboard settings. See xkeyboard-config(7). The preferred
           way to configure the keyboard is via the configuration file, see sway-in‐
           put(5).

       The following environment variables are set by sway:

       DISPLAY
           If compiled with Xwayland support and Xwayland is not disabled by the con‐
           fig, this will be set to the name of the X display used for Xwayland.

       I3SOCK
           For compatibility with i3, specifies the path to the sway IPC socket.

       SWAYSOCK
           Specifies the path to the sway IPC socket.

       WAYLAND_DISPLAY
           Specifies the name of the Wayland display that sway is running on.

       XCURSOR_SIZE
           Specifies the configured cursor size.

       XCURSOR_THEME
           Specifies the configured cursor theme.

AUTHORS
       Maintained by Simon Ser <contact@emersion.fr>, who is assisted by other open
       source contributors. For more information about sway development, see
       <https://github.com/swaywm/sway>.

SEE ALSO
       sway(5) swaymsg(1) sway-input(5) sway-output(5) sway-bar(5) sway-ipc(7)

                                       2022-01-10                               sway(1)
