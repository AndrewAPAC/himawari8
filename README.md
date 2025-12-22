# himawari8
*Put near-realtime picture of Earth as your desktop background*

![24 hours long animation by /u/hardypart](https://i.giphy.com/l3vRnMYNnbhdnz5Ty.gif)

himawari8 is a Python 3 script that fetches near-realtime (10 minutes delayed)
picture of Earth as its taken by
[Himawari 8 (ひまわり8号)](https://en.wikipedia.org/wiki/Himawari_8) and sets it
as your desktop background.

## Execution

(*See below for detailed instructions*)

For Linux, set a cronjob (or systemd service) that runs every 10 minutes to automatically get the
near-realtime picture of Earth.

For Mac, use a Launch Agent.

For Windows, use a scheduled task.

## Supported Desktop Environments
### Tested
* Unity 7
* Mate 1.8.1
* Pantheon
* LXDE
* OS X
* GNOME 3
* Cinnamon 2.8.8
* KDE
* Windows

### Not Supported
* any other desktop environments that are not mentioned above.

## Configuration

```
usage: himawari8 [-h] [--version] [--auto-offset | -o OFFSET]
                  [-l {4,8,16,20}] [-d DEADLINE] [--save-battery]
                  [--output-dir OUTPUT_DIR] [--dont-change]

set (near-realtime) picture of Earth as your desktop background

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit
  --auto-offset         determine offset automatically
  -o OFFSET, --offset OFFSET
                        UTC time offset in hours, must be less than or equal
                        to +10
  -l {4,8,16,20}, --level {4,8,16,20}
                        increases the quality (and the size) of each tile.
                        possible values are 4, 8, 16, 20
  -d DEADLINE, --deadline DEADLINE
                        deadline in minutes to download all the tiles, set 0
                        to cancel
  --save-battery        stop refreshing on battery
  --output-dir OUTPUT_DIR
                        directory to save the temporary background image
  --dont-change         don't change the wallpaper (just download it)

```

Most of the time himawari8 can accurately detect your timezone if you pass the flag `--auto-offset`, although you may
also set it manually by `-o` (or `--offset`) flag. If your timezone is beyond GMT by more than 10 hours, use the closest
one (either `+10` or `-12`).

Increasing the level will increase the quality of the image as well as the time taken to download all the tiles and the
memory consumption. For instance choosing 20 will make himawari8 use ~700 MiB of memory at its peak and the image will
be around ~200 MB.

You should set a deadline compatible with your cronjob (or timer) settings to assure that script will terminate in X
minutes before it is started again.

You might use `--save-battery` to disable refreshing while running on battery power.

You might also ask himawari8 to not to change your wallpaper by `--dont-change`
if you would it to download the image and stop.

### Nitrogen
If you use nitrogen for setting your wallpaper, you have to enter this in your
`~/.config/nitrogen/bg-saved.cfg`.

```
[:0.0]
file=/home/USERNAME/.himawari/himawari-latest.png
mode=4
bgcolor=#000000
```

## Installation
* You need a valid python3 installation including the python3-setuptools package

```
# Install (from top level directory)
pip install .

# Test whether it's working
himawari8 --auto-offset    # No errors should be returned
```

### For Linux users

#### Set himawari8 to be called periodically using the provided systemd timer
```
# Copy systemd configuration
cp systemd/himawari8.{service,timer} ~/.config/systemd/user/

# Enable and start the timer
systemctl --user enable --now himawari8.timer
```

### For KDE Users
#### KDE 5.7+
To change the wallpaper in KDE 5.7+, desktop widgets must be unlocked. If you dom't want to leave them unlocked,
the pre-KDE 5.7 method can still be used.

To unlock desktop widgets ([from the KDE userbase](https://userbase.kde.org/Plasma#Widgets)):
> Open the Desktop Toolbox or the Panel Toolbox or right click on the Desktop - if you see an item labeled Unlock
> Widgets then select that, and then proceed to add widgets to your Desktop or your Panel.

#### Before KDE 5.7
> So the issue here is that KDE does not support changing the desktop wallpaper
> from the commandline, but it does support polling a directory for file changes
> through the "Slideshow" desktop background option, whereby you can point KDE
> to a folder and have it load a new picture at a certain interval.
>
> The idea here is to:
>
> * Set the cron for some interval (say 9 minutes)
> * Open Desktop Settings -> Wallpaper -> Wallpaper Type -> Slideshow
> * Add the `~/.himawari` dir to the slideshow list
> * Set the interval check to 10 minutes (one minute after the cron, also
>   depending on your download speed)

Many thanks to [xenithorb](https://github.com/xenithorb) [for the solution](https://github.com/xenithorb/himawaripy/commit/01d7c681ae7ce47f639672733d0f734574662833)!

### For Mac OSX Users

OSX has deprecated crontab, and replaced it with `launchd`. To set up a launch agent, copy the provided sample `plist`
file in `osx/org.boramalper.himawari8.plist` to `~/Library/LaunchAgents`, and edit the following entries if required

    mkdir -p ~/Library/LaunchAgents/
    cp osx/org.boramalper.himawari8.plist ~/Library/LaunchAgents/

* `ProgrammingArguments` needs to be the path to himawari8 installation. This *should* be `/usr/local/bin/himawari8`
by default, but himawari8 may be installed elsewhere.

* `StartInterval` controls the interval between successive runs, set to 10 minutes (600 seconds) by default,
edit as desired.

Finally, to launch it, enter this into the console:

    launchctl load ~/Library/LaunchAgents/org.boramalper.himawari8.plist

### For Windows Users

#### Method 1: Command Line (Recommended)
```cmd
schtasks /create /xml "windows\himawari8.xml" /tn "Himawari8 Wallpaper" /ru "%USERNAME%"
```
#### Method 2: Task Scheduler GUI
1. Open Task Scheduler (`Win + R`, type `taskschd.msc`)
2. Right-click "Task Scheduler Library" → "Import Task..."
3. Select `windows\himawari8.xml`
4. Change the user account to your username (may not be required)
5. Click OK

You can right click and Run the task to test it

## Uninstallation

```
# Either remove the cronjob
crontab -e
    # Remove the line
    */10 * * * * himawari8...

# OR if you used the systemd timer
systemctl --user disable --now himawari8.timer
rm $HOME/.config/systemd/user/himawari8.{timer,service}

# OR if you use windows. delete the Scheduled Task


# Uninstall the package
pip3 uninstall himawari8
```


`<INSTALLATION_PATH>` can be found using the command `which -- himawari8`.

If you would like to share why, you can contact me on github or
[send an e-mail](mailto:a.lister.hk@gmail.com).

## Attributions
Thanks to *[MichaelPote](https://github.com/MichaelPote)* for the [initial
implementation](https://gist.github.com/MichaelPote/92fa6e65eacf26219022) using
Powershell Script.

Thanks to *[Charlie Loyd](https://github.com/celoyd)* for image processing logic
([hi8-fetch.py](https://gist.github.com/celoyd/39c53f824daef7d363db)).

Obviously, thanks to the Japan Meteorological Agency for opening these pictures
to public.
