# OSINT - Open Source INTelligence
# [MYOsintbadge](https://tryhackme.com/ixDamien/badges/ohsint)
## OBJ - Get as much information from a particular image.


### Using [exiftool](https://github.com/exiftool/exiftool#)
``` exiftool WindowsXP.jpg``` 
gave so much information about the image such as the copyright/author , GPS location and so on.

``` strings WindowsXP.jpg | less ```
### Digging into the copyright
found the copyright on google and it lead to a wordpress website for blog,github ,twitter.
the copyright is **0woodflint**  , searched him and i got.\

<img width="691" height="429" alt="image" src="https://github.com/user-attachments/assets/2954a1d7-65b4-476a-bff8-66feef3f2e80" />\

### looking into his github 
<img width="577" height="280" alt="image" src="https://github.com/user-attachments/assets/3a48e800-8552-4e74-80c8-bbb7ee941c4e" />\

### looking into his twitter 
found the BSsid on his x.  
the BSsid ( basic service set identifier) : it is a unique MAC address of the wifi access point.  
the Ssid ( service set identifier) : is simply the wifi network name ( airportwifi ), then i search the bssid on wiggle.com to get the location of the wifi and the name of the wifi which is the SSID.  
<img width="602" height="614" alt="image" src="https://github.com/user-attachments/assets/f0ca8d7d-3cb6-4c69-9305-de441c86f904" />



### HIS BLOG : Oliver blog website. 
checking the page thoroughly , i norice and unusual string after that to be sure.inspected the code and find out the unusual string and i saved it , i guess it is a password for something(maybe).
<img width="846" height="513" alt="image" src="https://github.com/user-attachments/assets/fc63d193-d333-41d9-88e7-4659affd420e" />
________________________________________________________________________________________________________
<img width="846" height="513" alt="image" src="https://github.com/user-attachments/assets/70a59a25-3b85-4e68-bc7d-256483b4922c" />
