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

# Dialplan
The included dialplan.xml gives some examples available. The dialplan file tells the handset how long to pause before dialling a number once it has been entered.

# Optional files
CTLSEP[MAC].tlv, ITLSEP[MAC].tlv, ITLFile.tlv can be empty

