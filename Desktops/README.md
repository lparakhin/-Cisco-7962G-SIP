Background Images
The list of available background images is specified in a file called List.xml in a Desktops/WIDTHxHEIGHTxDEPTH directory that the phone downloads from the provisioning server. The width, height and depth are determined by the capabilities of the phone's display. A maximum of 50 <ImageItem> entries can be specified.

<CiscoIPPhoneImageList>
ImageItemDefines a user-selectable background image. Both full-size and thumbnail images must be in PNG format. Note: while TFTP is the only valid scheme that can be specified, the phone will automatically use HTTP provisioning if enabled.

Image	The preview image to display when previewing the list of available background images.
URL	The full-size image to download.
  <ImageItem Image="TFTP:Desktops/WIDTHxHEIGHTxDEPTH/PREVIEW.png" URL="TFTP:Desktops/WIDTHxHEIGHTxDEPTH/IMAGE.png" />
  ...
</CiscoIPPhoneImageList>
