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

## Freeing up memory

```
free -h
```

```
cat /proc/meminfo
```

```
tail /boot/config.txt
```

```
# NOOBS Auto-generated Settings:
hdmi_force_hotplug=1
# start_x=1 will reserve 128Mb for the camera
start_x=0
# gpu_mem=128 will reserve 128Mb for the gpu
gpu_mem=16
```
