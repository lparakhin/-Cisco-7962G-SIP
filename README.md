# -Cisco-7962G-SIP
Cisco 7962G SIP Configuration Files and firmware (for 3rd party PBXs)

## TFTP/DHCP Configuration
During boot the handsets will discover the TFTP server via DHCP option 66 and/or DHCP option 150. For this example I set option 66 of our VoIP VLAN to the local IP address of our TFTP server. All of the configuration files, firmware files and other customisation files reside in the root directory of the TFTP server, in this case it was \TFTPBOOT. Download portable TFTP server here http://tftpd32.jounin.net/tftpd32_download.html, set IP address and option.

![image](https://user-images.githubusercontent.com/78740726/206854440-2cec49d0-ea0d-44bd-8079-42669cf0a410.png)

# Reset

## Soft Reset

The code for the soft factory reset is: 123456789*0# 
A soft reset does the following: 
5. deletes key files on the phone including /etc/init.tab 
6. reboots 
7. phone recovers default loads file from TFTP server

## Total Hard Reset

The code for the total hard reset is: 3491672850*# A total hard reset formats /flash0 blowing away everything except CNU. All settings and personalization of the phone is removed.

## Hard Reset with Network Setting Preservation

The code for the network preservation hard reset is: *7412369#085 And it also does a complete format of /flash0 — yet it preserves the network settings so that after the format, any static IP and other network settings are preserved. The settings are stored in volatile RAM, so it is important to not remove power to the phone during this process.

# Base Configuration Files

Each handset will require an SEP configuration files. The filenames must be SEP000000000000.cnf.xml where the 000000000000 is the MAC address of the Cisco handset and XMLDefault.cnf.xml.

### [XMLDefault.cnf.xml](../master/XMLDefault.cnf.xml) - Main System Config

You only need to change one line in this file for the 7962:

`<processNodeName>PUT IP OF PHONE SERVER HERE</processNodeName>`

But you should also check that the load information matches the firmware you downloaded:

`<loadInformation model="Cisco 7962">SIP42.9-4-2SR3-1S</loadInformation>`

The SIP42.9-4-2SR3-1S is taken directly from the SIP42 loads file name.  If you have other models of phone with different firmware you can add multiple loadInformation lines to cover this.

In theory you can also set the directory and services URL for all phones in this config file however be aware that not all phones support the same set of Cisco XML elements so if you're mixing models you might want to keep this config to each specific phone.

### [SEPMACADDRESS.cnf.xml](../master/SEPMACADDRESS.cnf.xml) - Phone Specific Config

This is where the bulk of the phone settings live.  You must rename this file to match the MAC address of the phone you're configuring.  You can find this from the sticker on the back of the phone or via Settings --> Model Information --> MAC Address.  For example my nearest phone has a MAC of 00270DBD73DD so I would rename the file SEP00270DBD73DD.cnf.xml.

These files aren't well documented, various guides have different explanations.  There doesn't seem to be anything useful available from Cisco.

At the minimum look into these lines:
  * **sshUserId** - Set SSH user login name here
  * **sshPassword** - Password for SSH (once logged in it asks you to login again as a phone user, use debug/debug for useful access)
  * devicePool / dateTimeSetting / ntps / ntp / **name** - Set the IP address of an NTP server to help the phones keep the correct time
  * callManagerGroup / members / member / callManager / **processNodeName** - Set the IP address of FreePBX here

The sipLines section is probably where you want to spend most of your time, this lays out the various labels on the phone and has the extension registration name and password:

  * sipLines / line / **featureLabel** - This is the Line label that shows to the left of the line button
  * sipLines / line / **name** - This is the extension number the phone needs to use
  * sipLines / line / **displayName** - This is how the phone name will be displayed when phoning someone else
  * sipLines / line / **authName** - This is what the phone will use to register with FreePBX (the extension number)
  * sipLines / line / **authPassword** - This is the extension password in FreePBX.  
  
  **The password must not be longer than 12 characters.**  The default FreePBX passwords are too long for the phone and you'll get config parse errors if you try to use them.  Please note, different guides mention different maximum lengths, as far as I can tell this varies based on whatever phone model and firmware version you're using, 12 worked for me but try and use the maximum length you can for security reasons (its not fun having massive phone bills).

  * sipLines / line / **contact** - This is the phone extension number, I'm not sure exactly where this appears on the phone

  The 7942s have 2 lines you can use, you specify this by copying the entire line section and changing the button="1" bit to button="2".  If you have a phone with 4 or more lines you simply increment the button="X" for whatever line you're configuring.

  E.g.

```xml
<sipLines>
  <line button="1">
    ...
  </line>
  <line button="2">
    ...
  </line>
</sipLines>
```

  * commonProfile / **phonePassword** - This is the password required on the phone to enter the unlocked settings menu.  
  
  Bear in mind you'll have to use the phone keypad to "type" this so perhaps don't be too paranoid with 24 digit passwords.  You access the unlocked settings by keying `**#` on the phone when in the settings menu.  You'll then get a message "settings unlocked".  
  
  The settings themselves are fairly boring, you can see some extra information but can't change anything worth messing with.
  
  * **loadInformation** - This needs to match the firmware name, the same as in the XMLDefault.cnf.xml file
  * **directoryURL** - This is the entry point for the phonebook integration via the Directory key on the front of the phone.  You'll need to change the IP Address to that of your FreePBX box (see phonebook integration below)
  

# Dialplan
The included dialplan.xml gives some examples available. The dialplan file tells the handset how long to pause before dialling a number once it has been entered. Simple dialplan:
```xml
<DIALTEMPLATE>
<TEMPLATE MATCH="*" Timeout="3"/>
</DIALTEMPLATE>
```

# Optional files
CTLSEP[MAC].tlv, ITLSEP[MAC].tlv, ITLFile.tlv can be empty

