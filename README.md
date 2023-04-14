# sleepwalk-phosh
Bash system service to periodically wake up Linux phones for notifications to arrive. The goal of this is to get a better battery time while still being able to reliably receive notifications. Currently, only calls, SMS and the system clock will make the phone wake up when deep sleep is used.

By default, its behavior is the following: it waits 1 minute for the mobile environment and dbus to load, then if these conditions aren't met, it will make the phone sleep for 30s, wake for another 30s (wake time is always the same); and then sleep again, adding 30s to the previous sleep time to a maximum time of 10 minutes. The LED will turn constant red during sleep and blinking green during wake time. These are the mentioned conditions:

- If screen is on.
- If there's any suspend inhibitors.
- If the modem is offline. (Only applicable to the PinePhone(Pro) version)
- If there's a call active
- If the phone is charging.
- If wlan0 is in AP mode.

If any of this conditions are met, the phone won't sleep and instead it will wait 1 minute before checking again. By default it is assumed that if you install the PinePhone(Pro) version, the modem-reset script is going to be used to restart the modem when it crashes, automatically entering the pincode and re-enabling mobile-data connectivity if it was enabled before crashing.

There's also another branch for the Oneplus 6 which doesn't have this problem.

If you want the phone to sleep even while having the charger plugged in: flip the variable SLEEPCHARGE from the top of the script from 0 to 1.

When new notifications are available, it will trigger sound and vibration (depending on your notification profile: active, vibration only or silence) and the LED color will turn to blue (constant blue during sleep; blinking blue during wake-time).

Added PinePhone's keyboard case support: if they keyboard's battery is present, to check if the charging cable is plugged in, it will see if the keyboard's battery is charging instead of the internal one. This allows the script to sleep even if the internal battery is charging as long as the keyboard one isn't.

(DISCLAIMER: WHEN THE KEYBOARD IS ATTACHED, PLEASE ONLY CHARGE THE PHONE AND KEYBOARD FROM THE KEYBOARD'S PORT TO AVOID ANY POTENTIAL DAMAGE TO THE KEYBOARD AS WELL AS THE PHONE AND THEIR BATTERIES. You can use the phone's port for anything other than charging AS LONG AS the keyboard ISN'T providing energy to the internal battery. For more information about this, please refer to https://xnux.eu/pinephone-keyboard/faq.html)

How to install and use:
- Save the 'sleepwalk', 'sleepwalk-notifier' and optionally the 'modem-reset' scripts to /usr/local/bin and make them executable

- Disable deep sleep in your desktop or mobile environment, if you plan on using multiple, you'll need to do this on all of them

- Systemd distros (Such as Mobian, Manjaro, or Arch): 
    - Copy the systemd service file 'sleepwalk.service' to '/etc/systemd/system/'
    - Execute 'sudo systemctl enable --now sleepwalk' to enable and start sleepwalk and optionally do the same for modem-reset if you are using the PinePhone(Pro) version: 'sudo systemctl enable --now modem-reset'
    
- Openrc distros (Such as PostmarketOS):
    - Copy the openrc service file 'sleepwalkrc' to '/etc/init.d'
    - Execute 'rc-update add sleepwalkrc' and 'service sleepwalkrc start' to enable and start the service
    
~~Sadly the script only works for systemd and phosh on the regular pinephone, but I do plan on making it compatible with everything eventually.~~
Guess what...  ;)

Known issues:
~~- The PinePhone modem goes off by itself whenever he wants, so I need to implement another script that keeps it running if it goes out. (WIP, last piece of the puzzle to be able to receive notifications reliably)~~

Not anymore, that's what modem-reset does

Dependencies:
~~- phosh (any other gnome based DE should work, I think. Untested though)~~
- A desktop environment, LOL
- rtcwake
- screen

## Important

Change the variable "USERNAME=" at the top of sleepwalk to your own user's name so that the notification feedback works.
For modem-reset, also change the PINCODE variable at the top of the modem-reset script to match yours.


## About the keyboard case

If you are experiencing charging problems with the keyboard case like I did, you might want to check out this (https://xff.cz/git/pinephone-keyboard/tree/) and this (https://codeberg.org/phalio/ppkbbat-tools). While using ppkbbat-tools the automatic mode could work for you or not, just read the documentation and try until you get it right so that both PinePhone and keyboard charge accordingly.


## About modem-reset

modem-reset checks for a network connection named 'mobiledata' to re-enable mobile data connectivity, so you need to change your current data connection name to 'mobiledata'. To find which one of them is your data connection, go to settings and turn on mobile data. Then, on the terminal, execute 'nmcli c show --active' to see your active connections. Search for the one that has the type 'gsm' and device 'cdc-wdm0' and note down its UUID. Last step is to change its name executing 'sudo nmcli c mod <YOUR_UUID_HERE> connection.id mobiledata'. That's it!

Also don't forget to change the pincode variable in the script to your own pincode.


## EXTRAS!

I included some optional scripts I personally made and use every day to daily drive Linux phones. You might want to use them in addition to sleepwalk or on their own. I hope these are useful to you! <3


## TODO

~~- Add support for the PinePhone Pro.~~
~~- Make the script universally compatible with every DE.~~

;)


##Porting to other devices

Porting is fairly simple. Grab the Oneplus 6 version as it should be simpler to adapt (the PinePhone version checks for 3 batteries, the battery case, PinePhone one and Pro, the PinePhone also has 3 single-color LEDs instead of a single RGB one).

To adapt it to your device, you only need to find out your screen device's path, battery path and LED path and replace them on the script:

On the SLEEPCHARGE function, look for the line:

if [ "$(cat /sys/class/power_supply/bq27411-0/status)" == "Charging" ]; then

And just replace the path to your battery path. Do the same thing for the screen on the can_sleep function and the LED on the LED functions:

if [ "$(cat /sys/class/backlight/ae94000.dsi.0/bl_power)" = "0" ]

---------------------------------------------------------------------

For the LED, you might need to find out which values are needed to turn the LED red, blue and green, and adjust them accordingly in the led_sleep, led_wake and led_disable functions.


## Contributing

If you want to contribute the project you can either donate me at https://ko-fi.com/l0v3p01s0n, spread the word so more people know about the project (I could get some contributors or some useful feedback that way) or just help me with the code yourself! Whatever the case, please open up a new issue if you want to suggest changes/improvements or report bugs.
