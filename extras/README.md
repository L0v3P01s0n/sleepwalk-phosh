# Extra scripts


## playerguard

GNOME script to inhibit sleep if a video/audio player is running. To install it simply copy the binary to /usr/local/bin, add it execute permissions and choose either the openrc service file or the systemd one. Enable and start the service and you should be good to go, but before that change your username and/or group to match your user on the service file. Dependencies:

- playerctl


## call-reset

call-reset checks every 5 seconds to see if you are currently in a call. If you are it will restart pulseaudio immediately after the call is finished. Why? Because I noticed a bug that freezes the entire phone if you were to plug your headphones in right after a call. For some reason it DOES change the output sound device to the internal speaker again as usual, but for a long time if another audio device gets connected in that period of time, the entire phone seems to run extremely laggy, forcing you to force a shutdown by holding down the power button. Restarting pulseaudio right after a call seems to resolve this issue, but similar freezing bugs still occur if you have headphones plugged in BEFORE the call occurs (I hope I knew how to fix that one).

Install it by copying call-reset to '/usr/local/bin/', call-reset.service to '/usr/lib/systemd/user/' and executing 'systemctl --user enable call-reset && systemctl --user start call-reset'


## it-reconnect

it-reconnect is just a simple script to try to reconnect to your PineTime/InfiniTime smartwatch automatically if bluetooth is on and the device is not connected. I made this because while using sleepwalk I noticed the phone will not try to reconnect to the watch by itself after a long time sleeping (it does if it sleeps shortly). This script ASSUMES you are using itd to connect and sync to your watch (https://gitea.arsenm.dev/Arsen6331/itd). If you have a PineTime/InfiniTime and aren't using itd (InfiniTime Daemon)... boy you are missing out! It's by far the best way right now to sync information between InfiniTime and the PinePhone, as well as relaying notifications, call notifications, music control and more. Please do yourself a favor and use it XD

To install it, same deal: copy it-reconnect to '/usr/local/bin/' and it-reconnect.service to '/usr/lib/systemd/user/'. Execute 'systemctl --user enable it-reconnect && systemctl --user start it-reconnect'


## ssh_inhibitor_bashrc

ssh_inhibitor_bashrc is just a simple SSH suspend inhibitor, useful for developers or people that are constantly doing stuff in the terminal remotely. Just copy its 3 lines of code ignoring the comment and paste them into your '~/.bashrc'. Now your phone won't go to sleep while being connected to it via SSH! Pretty useful, huh?
