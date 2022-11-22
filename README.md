# sleepwalk-phosh
"Proof-of-concept" bash script to periodically wake up the phone to check notifications (such as messenger or e-mail notifications). The goal of this is to get a better battery time while still being able to receive web-based notifications. Currently, only calls and SMS will make the phone wake up, when deep sleep is used.

By default, the script will make the phone sleep within 60 seconds, when the screen is turned off (sleep mode will not apply while the screen is on and will make the phone sleep for 30 seconds for the first time (if not interrupted by pressing the power button to wake the phone). It will then keep the phone awake for 30 seconds and, as long as the user does not interact with the device, put the phone immediately back to sleep mode for a longer time, in 30-second steps, up to a maximum of 10 minutes. If the screen is turned on, sleep time will reset to the initial value. You can change the durations directly at the top of the script. While the phone is sleeping, the RGB LED will light up constantly red and when it is awake for the short period of time, it will be a blinking green. The LED will be turned off, when the phone screen is enabled (for example by pressing the power button). When new notifications are available, it will trigger sound and vibration (depending on your notification profile) and the LED color will turn to blue (constant blue during sleep; blinking blue during wake-time).

How to install and use:
- Save the 'sleepwalk' and 'sleepwalk-notifier' scripts to /usr/local/bin
- Disable deep sleep in your desktop managers settings such as phosh
- Systemd distros (Such as Mobian, Manjaro, or Arch): 
    - Copy the systemd service file 'sleepwalk.service' to '/etc/systemd/system/'
    - Execute 'sudo systemctl enable --now sleepwalk' to enable and start the service
- Openrc distros (Such as PostmarketOS):
    - Copy the openrc service file 'sleepwalkrc' to '/etc/init.d'
    - Execute 'rc-update add sleepwalkrc' and 'service sleepwalkrc start' to enable and start the service

Known issues:
- The modem goes off by itself whenever he wants, so I need to implement another script that keeps it running if it goes out.

Dependencies:
- rtcwake
- screen
