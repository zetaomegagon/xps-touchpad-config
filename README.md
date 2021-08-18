# Overview
This method of configuring the Dell XPS 2021 touchpad buttons utilizes a bash script called `xps-touchpad-button-config` and a corresponding autostart file called `xps-touchpad-button-config.desktop`. In a desktiop environment like Gnome or KDE, which are aware of the [Freedesktop Desktop Entry Specification](https://specifications.freedesktop.org/desktop-entry-spec/latest/), the correctly placed `*.desktop` file will trigger the script upon user login.

# Instructions
1. Make sure you are logged into GNOME using Xorg. You should be able to configure this on your login
page by selecting `Gnome on Xorg` or similar.

2. Set touchpad to use **Click Areas** in Gnome (dunno about KDE).

3. Make sure both files have the executable bit set:
```
ls -al xps-touch*
...
chmod +x xps-touchpad-config{,.desktop}

(^ don't miss the comma)
```

4. Place `xps-touchpad-button-config` in `$HOME/bin`, or somewhere else, but you will need to edit the corresponding `*.desktop` file to make it aware of the change.

5. Place `xps-touchpad-button-config.desktop` in `$HOME/.config/autostart`.

6. Edit the `*.desktop` file to have the `Exec` path reflect the `xps-touchpad-conf` script's actual location 

# Notes
The actual script, while lightweight, has a lengthy comment. Mostly it details the behavior of the piped command substitution bound to `$dev_id`, for those who may not be as familiar with bash and bash debugging.

On thing that I may have not clarified enough is that this method modifies buttons on a physical device, where we shouldn't necessarily assume that any two devices that look the same are, in fact, the same. That being said, I derived the piped command substitution on my machine-- your's may differ.

In the event that this method doesn't work right away, please use a systematic approach to debugging:

#### File locations and permissions
* make sure the files are in the respective locations you want
* make sure the `*.desktop` file has an `Exec` that points to the script
* make sure **both** the `*.desktop` **and** `script` files are executable

#### The script
Most likely any issue here is going to be with the piped command subsititution, re: `$(xinput list | command1 | command2 | commandN)`, where your system is different from mine. In this case, you should run each portion of the overall pipeline in that substitution on the command line to see the resulting output; then modify the script to your actual output.

For instance `xinput list --long | grep -i dll` returns the following for me:
```
[ebeale@nekko ~]$ xinput list --long | grep -i dll
⎜   ↳ DLL0945:00 06CB:CDE6 Mouse                id=10   [slave  pointer  (2)]
⎜   ↳ DLL0945:00 06CB:CDE6 Touchpad             id=11   [slave  pointer  (2)]
```
Normally, I'd show an example with just the first command run with it's output, but `xinit list --long` is too long an output, and I'm too lazy to copy-paste the proper section.

Just make sure you are testing in an iterative manner, beginning with the first command in the pipe.
