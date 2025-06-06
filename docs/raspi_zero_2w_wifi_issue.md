# Raspberry pi zero 2w WiFi issue

To fix WIFI on raspi zero 2w on 64 bit bookworm OS:
Edit file `cmd.txt` in bootfs partition and add to the end:
```
brcmfmac.feature_disable=0x82000
```
Please check this URL for more information https://forum.mikrotik.com/viewtopic.php?p=1134453
