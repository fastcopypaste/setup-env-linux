# setup-env-linux

### 1. Setup VNC server with XFCE

Run command below line by line:

```
sudo apt update
sudo apt install xfce4 xfce4-goodies
sudo apt install tightvncserver
```

Init `vnc` server:
```
vncserver
```
Tt will ask you create new `password` (the password you will use to login) and `view-only password` (the password for view-only, you can not click, etc, ...)

After that:
```
vncserver -kill :1
mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
```
Then we create new `xstartup` file
```
nano ~/.vnc/xstartup
```
Paste this text:
```
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```
Save this file then:
```
sudo chmod +x ~/.vnc/xstartup
vncserver
```

You can connect to your vnc now using any vnc client, for me, I use `vncviewer` in Linux.

To make the `vnc` better, we can make `vncserver` run as a service.

```
sudo nano /etc/systemd/system/vncserver@.service
```
Paste this content:
```
[Unit]
Description=Start TightVNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User={username}
Group={group}
WorkingDirectory=/home/{username}

PIDFile=/home/{username}/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 :%i
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target

```
where:

{username} is your username, you can get it with ```echo $USER```

{group} is your group, you can get a list of groups that your user current belong to by command:
```
groups {$USER}
```
You should choose the groups with privilege enough to run our `vncserver` in startup, for me it is: `google-sudoers`.

It is default group in Google Cloud and can run any `sudo` command without password.

Then:

```
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1.service
vncserver -kill :1
sudo systemctl start vncserver@1
```

Verify by:
```
sudo systemctl status vncserver@1
```
You should see the status is Active (running)


### 2. Create swap space

The first we need to check if `swap` is enabled or not
```
swapon --show
```
If the output is blank then it is good to following the instruction.

a. Create a swap file:

```
sudo fallocate -l {MemoryNumber}G /swapfile
```

Where {MemoryNumber} is about double the amound of your RAM.

You can check the swap file was created by type: ```ls -lh /swapfile```

b. Configure the swap file:

```
sudo chmod 600 /swapfile
sudo mkswap /swapfile
```

The we can enable the swap file:

```
sudo swapon /swapfile
```

Verify it by:

```
sudo swapon --show
```
or
```
free -m
```
