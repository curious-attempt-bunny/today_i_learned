<img src="https://i.pinimg.com/736x/9e/b2/c2/9eb2c214b911f382ebc7825768226285--gary-larson-the-far-side.jpg" alt="drawing" width="100"/> 

# Github

## Pushing code to mutliple github accounts

See https://gist.github.com/jexchan/2351996/.

```
#activehacker account
Host github.com-activehacker
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_rsa_activehacker

#jexchan account
Host github.com-jexchan
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_rsa_jexchan
```

and, possibly

```
If your ssh keys don't all show up with
ssh-add -l
you have to run
ssh-add ~/.ssh/yourkey.rsa
```

# Raspberry pi

## Preparing an SD image

```
diskutil list
```

```
diskutil unmountDisk /dev/disk2
sudo dd bs=1048576 if=path_to.img of=/dev/disk2 &
...
sudo kill -INFO $(pgrep ^dd)
```

## Locate your Pi

```
sudo arp-scan -l | grep "Raspberry Pi Foundation" | grep -v DUP
```

...with sshd setup

```
sudo arp-scan -l | grep "Raspberry Pi Foundation" | grep -v DUP | cut -f1 | xargs nmap -P 22
```

## Join WiFi from the commandline

`/etc/wpa_supplicant.conf`:
```
{
  network="<ssid>"
  psk="<password>"
}
```

```
killall wpa_supplicant
wpa_supplicant -B -iwlan0 -c/etc/wpa_supplicant.conf
dhclient wlan0
```

## Freeing up memory

```
free -h
```

```
cat /proc/meminfo
```

`/boot/config.txt`:
```
...
# NOOBS Auto-generated Settings:
hdmi_force_hotplug=1
# start_x=1 will reserve 128Mb for the camera
start_x=0
# gpu_mem=128 will reserve 128Mb for the gpu
gpu_mem=16
```

## Running a minecraft server

See https://pimylifeup.com/raspberry-pi-minecraft-server/.

```
wget https://hub.spigotmc.org/jenkins/job/BuildTools/lastSuccessfulBuild/artifact/target/BuildTools.jar
```

```
java -Xmx750M -jar BuildTools.jar --rev 1.14.4
```

...

```
sudo systemctl status minecraftserver.service
```

## Setting up a touchscreen-kiosk

https://desertbot.io/blog/raspberry-pi-touchscreen-kiosk-setup

```
sudo raspi-config # find Console Autologin
sudo apt-get install --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox
sudo apt-get install chromium-browser
sudo nano /etc/xdg/openbox/autostart
```

Add to `/etc/xdg/openbox/autostart`:
```
# Remove exit errors from the config files that could trigger a warning
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chromium/'Local State' 
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type":"[^"]\+"/"exit_type":"Normal"/' ~/.config/chromium/Default/Preferences

while true:
do
  chromium-browser --noerrdialogs --disable-infobars --kiosk $KIOSK_URL --check-for-update-interval=31536000
done
```

```
sudo nano /etc/xdg/openbox/environment
```

Add to `/etc/xdg/openbox/environment`:
```
export KIOSK_URL=https://spotify.com # for example
```

## Force killing chromium-browser

```
pkill -2 chromium-browse # yes, without the trailing "r"
```

## Forcing touchscreen to turn off (it will still wake when you touch it)

```
export DISPLAY=:0
xseq dpms force off
```

## Crontab for supressing kiosk usage at night

```
30,40,50 21 * * * /home/pi/quiet.sh
*/10 0-6,22-23 * * * /home/pi/quiet.sh
```

quiet.sh:
```
export DISPLAY=:0
xset dpms force off
pkill -2 chromium-browse
```

## Enabling Spotify DRM media playback (maybe Netflix too)

NOTE: This means untrusted binaries! Not secure!
These steps work for Raspberry Pi4 on Raspbian Buster. 

https://blog.vpetkov.net/2020/03/30/raspberry-pi-netflix-one-line-easy-install-along-with-hulu-amazon-prime-disney-plus-hbo-spotify-pandora-and-many-others/

```
sudo apt update
# sudo apt full-upgrade # maybe?
sudo apt install libwidevinecdm0
wget -q --no-check-certificate https://pi.vpetkov.net/libwidevinecdm.so
wget -q --no-check-certificate https://pi.vpetkov.net/manifest.json
chmod 755 libwidevinecdm.so && chmod 644 manifest.json 
mkdir -p ${HOME}/.config/chromium-browser/WidevineCdm
echo '{"Path":"/opt/WidevineCdm"}' > ${HOME}/.config/chromium-browser/WidevineCdm/latest-component-updated-widevine-cdm
sudo mkdir -p /opt/WidevineCdm/_platform_specific/linux_arm && sudo mv -f manifest.json /opt/WidevineCdm && sudo mv -f libwidevinecdm.so /opt/WidevineCdm/_platform_specific/linux_arm 
```

# Kali / Pentesting

(It shouldn't need to be said, but: only use these on above-board scenarios, e.g. hackthebox.eu)

## Helpful links

* [penetration-testing-tools-cheat-sheet](https://highon.coffee/blog/penetration-testing-tools-cheat-sheet/)
* [vulnhub](https://www.vulnhub.com/)
* [hack the box](https://www.hackthebox.eu)
* [offensive guide - first step](https://0xsp.com/offensive/offensive-guide)
* [offensive guide - second step](https://0xsp.com/offensive/offensive-guide-second-step)

## What can a user do with sudo?

`sudo -l`

## What encryption is used on a file?

See https://www.mogozobo.com/?p=1528.

```
    #/bin/bash

    CIPHER_LIST=openssl list-cipher-commands

    for cipher in $CIPHER_LIST; do
    openssl end -d -${cipher} -in $1 -pass pass:$2 > /dev/null 2>&1
    if [[ $? -eq 0 ]]; then
    echo "Cipher used was $cipher. unencrypting $1"
    openssl enc -d -${cipher} -in $1 -out $3 -pass pass:$2
    exit
    fi
    done
```

# VirtualBox

## No ipv4 address

Having seen an ipv6 address but no ipv4 address; Try attaching to "NAT" for your VirtualBox network device instead of "Bridged Adapter".
