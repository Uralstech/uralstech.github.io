---
layout: post
title:  "setting up Deskflow to open up on start on macOS"
tags: [Tutorial]
---

I was messing around with Deskflow on a Mac mini and since no one else has done a tutorial on
setting it up to run *before* you sign in, I thought I'd write it down.
<!--more-->

Basically, I wanted to use my Windows laptop's keyboard and trackpad as the main input for my Mac mini
since I'm used to it + it'd save desk space. If you don't know what [Deskflow](https://github.com/deskflow/deskflow/)
is, it just allows you to use one computer's input devices on another. It's been working good so far for me.

To get started, connect your PC and Mac through Deskflow if you haven't done it before. This is to set up the TLS
certificate verification between the two. Now, copy the `tls` folder in `~/Library/Deskflow` to `/Library/Deskflow`.
This is a workaround since `/Library/Deskflow` is where Deskflow will look for the TLS configs when running as root.
It's wonky, but it's working fine for me :P

I was able to do this from Finder without issues, but you can also do it from the command line (unverified code, made by Gemini):
```sh
sudo mkdir -p /Library/Deskflow
sudo cp -R ~/Library/Deskflow/tls /Library/Deskflow/
sudo chown -R root:wheel /Library/Deskflow/tls

# Set appropriate permissions (e.g., read-only for files, executable for directories)
# For simplicity, 755 for directories and 644 for files is often safe
sudo find /Library/Deskflow/tls -type d -exec chmod 755 {} \;
sudo find /Library/Deskflow/tls -type f -exec chmod 644 {} \;
```

I created a shell script to be run on startup since I wanted to run an extra command at start. By default,
the cursor movement when receiving input on macOS using Deskflow is pretty laggy unless you disable awdl0
(Apple Wireless Direct Link). This is a known issue and a workaround is documented in [Deskflow's wiki](https://github.com/deskflow/deskflow/wiki/Workarounds#macOS-laggy-cursor).

I created the script at `/usr/local/bin/start_deskflow.sh`:
```sh
#!/bin/sh
ifconfig awdl0 down
/Applications/Deskflow.app/Contents/MacOS/deskflow-core client --no-daemon --enable-crypto [replace this with your server's IP]
```

The script will disable awdl0 and start Deskflow in client mode and connect to the given IP. Now, make it executable:
`sudo chmod +x /usr/local/bin/start_deskflow.sh`

Now, we have to create the [launch daemon](https://support.apple.com/en-in/guide/terminal/apdc6c1077b-5d5d-4d35-9c19-60f2397b2369/mac).
I created it at `/Library/LaunchDaemons/com.deskflow.client.plist`:

Paste the following to the file in Nano:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.deskflow.client</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/start_deskflow.sh</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/tmp/deskflow.stdout.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/deskflow.stderr.log</string>
</dict>
</plist>
```

Now, make it accessible to the system, and load it:
```sh
sudo chown root:wheel /Library/LaunchDaemons/com.deskflow.client.plist
sudo chmod 644 /Library/LaunchDaemons/com.deskflow.client.plist
sudo launchctl load /Library/LaunchDaemons/com.deskflow.client.plist
```

If you want to modify the daemon, or want to stop it, run:
`sudo launchctl unload /Library/LaunchDaemons/com.deskflow.client.plist`

And that's it! If Deskflow is having issues connecting to the server, check the logs at `/tmp/deskflow.stdout.log`
and `/tmp/deskflow.stderr.log`.