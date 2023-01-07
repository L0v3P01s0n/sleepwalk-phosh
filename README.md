# sleepwalk-phosh
Bash system service to periodically wake up the PinePhone for notifications to arrive. The goal of this is to get a better battery time while still being able to reliably receive notifications. Currently, only calls, SMS and the system clock will make the phone wake up when deep sleep is used.

By default, its behavior is the following: it waits 1 minute for the mobile environment and dbus to load, then if these conditions aren't met, it will make the phone sleep for 30s, wake for another 30s (wake time is always the same); and then sleep again, adding 30s to the previous sleep time to a maximum time of 10 minutes. These are the mentioned conditions:

- If screen is on.
- If there's any suspend inhibitors.
- If the modem is offline.
- If the phone is charging.

If any of this conditions are met, the phone won't sleep and instead it will wait 1 minute before checking again. By default it is assumed that the modem-reset script is going to be used to restart the modem when it crashes, automatically entering the pincode and re-enabling mobile-data connectivity if it was enabled before crashing.

If you don't plan on using modem-reset or just having it disabled to enable it when you want to and you want the phone to sleep even if the modem isn't present, you can flip the variable MODEMRESET at the top of the script to 0 instead of 1. If you want the phone to sleep even while having the charger plugged in, same thing goes, but backwards: flip the variable SLEEPCHARGE from the top of the script from 0 to 1.

When new notifications are available, it will trigger sound and vibration (depending on your notification profile: active, vibration only or silence) and the LED color will turn to blue (constant blue during sleep; blinking blue during wake-time).

Added support for inhibiting suspend and going idle when mpv is running, as it is not detected as a video/audio player, as well as inhibiting only suspend when any other detected audio/video player is playing media. Also added keyboard case support: if they keyboard's battery is present, to check if the charging cable is plugged in, it will see if the keyboard's battery is charging instead of the internal one. This allows the script to sleep even if the internal battery is charging as long as the keyboard one isn't.

(DISCLAIMER: WHEN THE KEYBOARD IS ATTACHED, PLEASE ONLY CHARGE THE PHONE AND KEYBOARD FROM THE KEYBOARD'S PORT TO AVOID ANY POTENTIAL DAMAGE TO THE KEYBOARD AS WELL AS THE PHONE AND THEIR BATTERIES. You can use the phone's port for anything other than charging AS LONG AS the keyboard ISN'T providing energy to the internal battery. For more information about this, please refer to https://xnux.eu/pinephone-keyboard/faq.html)

How to install and use:
- Save the 'sleepwalk', 'sleepwalk-notifier' and 'playerguard' (modem-reset too if you plan to use it) scripts to /usr/local/bin

~~- Disable deep sleep in phosh~~
(No longer needed. When the service is started it will automagically disable it by itself, and turn it back on when the service is stopped)
- Systemd distros (Such as Mobian, Manjaro, or Arch): 
    - Copy the systemd service file 'sleepwalk.service' to '/etc/systemd/system/'
    - Execute 'sudo systemctl enable --now sleepwalk' to enable and start the service (optionally, do the same for modem-reset if you want to use it)
- Openrc distros (Such as PostmarketOS):
    - Copy the openrc service file 'sleepwalkrc' to '/etc/init.d'
    - Execute 'rc-update add sleepwalkrc' and 'service sleepwalkrc start' to enable and start the service

Known issues:
~~- The modem goes off by itself whenever he wants, so I need to implement another script that keeps it running if it goes out. (WIP, last piece of the puzzle to be able to receive notifications reliably)~~

Not anymore, if you use modem-reset :)

Dependencies:
- phosh (any other gnome based DE should work, I think. Untested though)
- rtcwake
- screen
- playerctl

## Important

Change the variable "USERNAME=" at the top of sleepwalk to your own user's name so that the notification feedback works.
If you plan on using modem-reset, also change the PINCODE variable at the top of the modem-reset script to match yours.


## About the keyboard case

If you are experiencing charging problems with the keyboard case like I did, you might want to check out this (https://xff.cz/git/pinephone-keyboard/tree/) and this (https://codeberg.org/phalio/ppkbbat-tools). While using ppkbbat-tools the automatic mode could work for you or not, just read the documentation and try until you get it right so that both PinePhone and keyboard charge accordingly.


## EXTRAS!

I included some optional scripts I personally made and use every day to daily drive my PinePhone. You might want to use them in addition to sleepwalk or on their own. I hope these are useful to you! <3


## TODO

- Add support for the PinePhone Pro.
- Make the script universally compatible with every DE.


## Contributing

If you want to contribute the project you can either donate me at https://ko-fi.com/l0v3p01s0n, spread the word so more people know about the project (I could get some contributors or some useful feedback that way) or just help me with the code yourself! Whatever the case, please open up a new issue to suggest changes/improvements or report bugs.
