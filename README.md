# For macOS
Just a list of commands and settings for whenever I do a fresh macOS installation, as well as fixes for common annoyances

### Privacy

A few are taken from the macOS-Security-and-Privacy-Guide:

Disable Spotlight Bing Search Suggestions in System Preferences, as well as Safari Suggestions in Safari Preferences.

Disable opening "Safe" files in Safari.

Enable the Develop menu safari( useful for opening pages in Chrome, go to Develop-> Open Page With)

Enable Secure Keyboard Entry in Terminal.

Disable loading Remote Images in Mail

Disable Captive Portal:
```
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.captive.control Active -bool false
```
### Display
Text Smoothing: Halfway in between subpixel antialiasing and greyscale antialiasing.
defaults -currentHost write -globalDomain AppleFontSmoothing -int 2

Disable Ambient Display: (http://www.mackungfu.org/el-capitan-s-crazy-gamma-adjustments-photographers-beware)

```
cd /System/Library/PrivateFrameworks/AmbientDisplay.framework/Versions/A/XPCServices/com.apple.AmbientDisplayAgent.xpc/Contents/MacOS/

sudo mv com.apple.AmbientDisplayAgent _com.apple.AmbientDisplayAgent
```
Enable HiDPI resolutions:
```
sudo defaults write /Library/Preferences/com.apple.windowserver.plist DisplayResolutionEnabled -bool true
```

Enable Subpixel antialiasing: https://github.com/Microsoft/vscode/issues/59887

Install SF Mono fonts, and use as default in VS Code: https://medium.com/@sobstel/how-to-use-apples-sf-mono-font-in-visual-code-studio-4c99e44ed2cb

Enable Dark Menu Bar, but not Dark Mode:

```
defaults write -g NSRequiresAquaSystemAppearance -bool Yes
defaults write com.apple.notificationcenterui NSRequiresAquaSystemAppearance -bool No

defaults delete -g NSRequiresAquaSystemAppearance
defaults delete com.apple.notificationcenterui NSRequiresAquaSystemAppearance
```

### Behaviour

Change Key repeat	to fast in System Preferences

Disable the Rotate gesture for the Trackpad in System Preferences

Show Hidden Files:
```
defaults write com.apple.finder AppleShowAllFiles YES
```

Globally disable auto-saving Documents to iCloud:
```
defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool false
```

TextEdit Old Behaviour, starts by showing empty text file:
```
defaults write com.apple.TextEdit NSShowAppCentricOpenPanelInsteadOfUntitledFile -bool false
```

Power Chime whenever your Power Supply is plugged in:
```
defaults write com.apple.PowerChime ChimeOnAllHardware -bool true;open /System/Library/CoreServices/PowerChime.app &
```

Stop Photos from opening every time you connect something with images:
```
defaults -currentHost write com.apple.ImageCapture disableHotPlug -bool NO
```
Disable automatic iDevice sync:
Go to iTunes and check `Prevent iPods, iPhones, and iPads from syncing automatically`


Go to Keyboard Preferences -> Disable spelling correction, smart quotes.
### Fixes
Stop .DS_Store Pollution on Network drives:
```
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
```

Disconnect from a broken SSH Connection:
`Enter` + `~` + `.`

Flush DNS Cache
```
sudo dscacheutil -flushcache;sudo killall -HUP mDNSResponder
```
Append Search Domain
```
sudo launchctl unload /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
sudo defaults write /Library/Preferences/com.apple.mDNSResponder.plist AlwaysAppendSearchDomains -bool YES
sudo launchctl load /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
```

Fix Launchpad
```
defaults write com.apple.dock springboard-columns -int 6;defaults write com.apple.dock springboard-rows -int 4;defaults write com.apple.dock ResetLaunchPad -bool TRUE;killall Dock
```

Allow Apps from Any Developers
```
sudo spctl --master-disable
```

Install App with Outdated xattr:
```
xattr -c /path/to/app.app
```

### Finder

Show all extensions:
```
defaults write NSGlobalDomain AppleShowAllExtensions -bool true
```
Get rid of Remote Disc
```
sudo defaults write /Library/Preferences/com.apple.NetworkBrowser EnableODiskBrowsing -bool false
```
Show Library in Home Folder:
Click Home folder, then the View Options dialog box, select Show Library Folder.	


### Software

Install brew from http://brew.sh

Install borgbackup from brew

If you have a mouse with side buttons, install Sensible Side Buttons

Install native display brightness controls (change f keys with functionflip)

Disable mouse acceleration for mouse
```
defaults write -g com.apple.mouse.scaling -1
```

Install XCode tools
```
xcode-select --install
```
## for linux
```
sudo systemctl disable ondemand
```

https://haydenjames.io/how-to-enable-unattended-upgrades-on-ubuntu-debian/

dpkg-reconfigure -plow unattended-upgrades

```
.hushlogin

```

use idle3 to disable head lock for WD RED.

disable systemd networking

disable snapd if you want

### ZFS
generate key
```
dd if=/dev/urandom of=key bs=32 count=1

xxd -p key | tr -d '\n' && echo

zpool create -o ashift=12 \
   -O acltype=posixacl -O dnodesize=auto -O normalization=formD \
   -O relatime=on -O xattr=sa -O encryption=aes-256-gcm \
   -O keylocation=prompt -O keyformat=hex \
   rust mirror sda3 sdb3
     
```
generate zvol
```
zfs create rust/shady_vms
chown yoonsik:yoonsik /rust/shady_vms
```
loading and unloading key + mounting
```
zfs load-key tank
zfs mount tank
zfs mount -a

zfs umount tank
zfs unload-key tank
```
recieving things over ssh
```
sudo zfs send -Rw tank/vms | ssh root@198.57.27.183 'cat - > test'

ssh root@198.57.27.183 'cat test' | sudo zfs recv tank/vms


```
## for windows
Disable diagnostic policy service
