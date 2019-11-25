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

# Kali

## Helpful links

* [penetration-testing-tools-cheat-sheet](https://highon.coffee/blog/penetration-testing-tools-cheat-sheet/)
* [vulnhub](https://www.vulnhub.com/)
* `sudo arp-scan -l | grep "Raspberry Pi Foundation" | grep -v DUP | cut -f1 | xargs nmap -P 22`
