# Check internet connection availability for ASUS RT-AX92U

For no reason my ASUS RT-AX92U router looses its internet connection. The only possibility is to reboot the device. It sometimes happens just after I leave the house, I guess it has something to do with the WiFi signal not properly being handed over or something like that?

Googling the problem and troubleshooting it by going over all possible settings in the control panel hasn't worked for me.

Using the following sources I came to a workaround:
- https://www.snbforums.com/threads/how-to-automatically-run-a-script-on-asus-routers-that-monitors-your-wan-link-will-reboot-the-router-if-wan-is-down-from-pov-of-a-non-linux-guy.74336/
- https://github.com/MartineauUK/Chk-WAN
- https://github.com/jacklul/asuswrt-scripts

I have had this running for about a year but since a few weeks it wasn't working anymore. I found a workaround in [this](https://github.com/jacklul/asuswrt-scripts/tree/master/asusware-usbmount) repo.

I'm documenting this for myself but maybe it can help somebody else.

## Steps

1. Enable SSH on router (Administration -> Enable SSH for LAN Only)
2. SSH into the router
3. Create a file `vi /jffs/scripts/init-check.sh` with the following contents:

```shell
#!/bin/sh

cru a internetcheck.sh "* * * * * /jffs/scripts/internetcheck.sh"
```

4. Create a file `vi /jffs/scripts/internetcheck.sh` with the following contents:

```shell
#!/bin/sh

ping -c5 google.com

if [ $? -eq 0 ]; then
    date +"%Y-%m-%d %r - internet connection UP" >> /jffs/scripts/check.log
    logger -s Internet connection UP
else
    date +"%Y-%m-%d %r - internet connection DOWN ... REBOOT" >> /jffs/scripts/check.log
    logger -s Internet connection DOWN - REBOOT
    reboot
fi
```

5. Run `nvram set script_usbmount="/jffs/scripts/init-check.sh"`
6. Run `nvram commit`
7. Put the `asusware.arm` directory on USB drive
8. Insert and leave this USB drive in router
9. Reboot the router

## Credits
I have only combined resources created by these awesome people:
- User Carme on SNBForums
- @MartineauUK
- @jacklul

Thanks all for putting these amazing resources online for everybody to use!