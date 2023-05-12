# For macOS
Just a list of commands and settings for whenever I do a fresh macOS installation, as well as fixes for common annoyances

### Privacy

A few are taken from the macOS-Security-and-Privacy-Guide:

Disable Spotlight Bing Search Suggestions in System Preferences, as well as Safari Suggestions in Safari Preferences.

Disable opening "Safe" files in Safari.

Enable the Develop menu safari (useful for opening pages in Chrome, go to Develop-> Open Page With)

Enable Secure Keyboard Entry in Terminal.

Disable Captive Portal:
```
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.captive.control Active -bool false
```

Disable desktop icons:
```
defaults write com.apple.finder CreateDesktop false
killall Finder
```

### Display

Disable Auto Brightness Display

Enable HiDPI resolutions:
```
sudo defaults write /Library/Preferences/com.apple.windowserver.plist DisplayResolutionEnabled -bool true
```

Use JetBrains Mono as default in VS Code

### Behaviour
Stop SSH from accepting password auth:
```
https://apple.stackexchange.com/questions/225231/how-to-use-ssh-keys-and-disable-password-authentication
```

Enable Sudo with TouchID!

Change Key repeat to fast in System Preferences

Disable the Rotate gesture for the Trackpad in System Preferences

Globally disable auto-saving Documents to iCloud:
```
defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool false
```

TextEdit Old Behaviour, starts by showing empty text file:
```
defaults write com.apple.TextEdit NSShowAppCentricOpenPanelInsteadOfUntitledFile -bool false
```

Terminal: follow mouse focus
```
defaults write com.apple.Terminal FocusFollowsMouse -boolean YES
```

Stop Photos from opening every time you connect something with images:
```
defaults -currentHost write com.apple.ImageCapture disableHotPlug -bool NO
```
Disable automatic iDevice sync:
Go to iTunes and check `Prevent iPods, iPhones, and iPads from syncing automatically`

Go to Keyboard Preferences -> Disable spelling correction, smart quotes.

Disable code signing: https://www.naut.ca/blog/2020/11/13/forbidden-commands-to-liberate-macos/

Stop App store notifications: https://apple.stackexchange.com/questions/409093/how-can-i-permanently-disable-app-store-notifications-in-big-sur

Stop macOS from waking up every 3 minutes (you can check logs `pmset -g log | grep -w Charge`): https://discussions.apple.com/thread/252276065
```
sudo pmset -a tcpkeepalive 0
sudo pmset -a powernap 0
sudo pmset -g assertions
```

In System Preferences -> General -> Enable "Ask to save before closing documents"

Archive macOS App Store apps:

```
getconf DARWIN_USER_CACHE_DIR
```

### Fixes
Stop .DS_Store Pollution on Network drives:
```
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
```

Stop AirDrop from interfering:

```
set Airdrop to No one
```

Allow Ethernet to have same IP as WiFi:
```
https://github.com/CoolCyberBrain/toggleairport
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

Install App with Outdated xattr:
```
xattr -c /path/to/app.app
```

sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.GameController.gamecontrollerd.plist
### Finder

Stop mDNS pollution:

```
sudo defaults write /Library/Preferences/com.apple.mDNSResponder.plist NoMulticastAdvertisements -bool YES
```

Disable NETBIOS
```
sudo launchctl disable system/netbiosd
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.netbiosd.plist
```

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

Reset Quick Look Thumbnails:
```
qlmanage -r cache
```

Found this tweet, https://twitter.com/vatsal_manot/status/1409264360594149380?s=20
Basically suggests the following to disable suggestd.

"cd ~/Desktop && touch Suggestions"
"Get Info" → set "Locked" to on
Delete ~/Library/Suggestions
Drag Suggestions (the file) into ~/Library

### Software

Install brew from http://brew.sh

Install borgbackup from brew

If you have a mouse with side buttons, install Sensible Side Buttons

Disable mouse acceleration for mouse?
```
defaults write -g com.apple.mouse.scaling -1
```

Install XCode tools
```
xcode-select --install
```

Disable Office 2016 Nag:

https://apple.stackexchange.com/a/365377

Change login wallpaper
https://macreports.com/how-to-change-the-login-screen-background-in-macos-big-sur/comment-page-1/?unapproved=155065&moderation-hash=0cd1c2b325f3e1bcf98bad8a5333304c#comment-155065

## for linux
```
sudo systemctl disable ondemand
```

https://haydenjames.io/how-to-enable-unattended-upgrades-on-ubuntu-debian/

dpkg-reconfigure -plow unattended-upgrades

```
.hushlogin

```
disable powersave:

sudo update-rc.d ondemand disable

use idle3 to disable head lock for WD RED.

disable systemd networking

disable snapd if you want
sudo apt purge snapd 
### network
```

# interfaces(5) file used by ifup(8) and ifdown(8)
# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d

allow-hotplug eno1
auto eno1
iface eno1 inet manual
    post-up ethtool -K eno1 tx off rx off gso off gro off tso off

auto br0
iface br0 inet static
    bridge_ports eno1
    bridge_stp off
    bridge_fd 0
    bridge_maxwait 0
    address 192.168.0.100/24
    gateway 192.168.0.1
```

### fix shutdown button --> libvirtd


nano /etc/systemd/logind.conf
nano /usr/lib/systemd/logind.conf.d/unattended-upgrades-logind-maxdelay.conf


InhibitorsMax=1

### disable ModemManager

### fix ssh dns
usedns No

Also, for scripting SSH, use -o BatchMode=yes -o ConnectTimeout=10

### ZFS
use `skdump` to analyze disks

generate key
```
dd if=/dev/urandom of=key bs=32 count=1

xxd -p key | tr -d '\n' && echo
```
zpool will create gpt partition and align it to 2048 * 512 byte sector. Even if is 4096 byte physical sector
```
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
chown -hR yoonsik:yoonsik /rust/shady_vms
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
Consistent for nvme imports
```
sudo zpool import -d /dev/disk/by-id -aN
```
## for windows
Disable diagnostic policy service
Disable certificate propgation service
Disable DNS Cache
https://social.technet.microsoft.com/Forums/windows/en-US/a04284f9-cf27-4f37-82fe-31255f70625f/how-to-disable-windows-10-dns-cache-services?forum=win10itpronetworking

Disable all protocols in network adapter settings

https://windowsreport.com/how-to-disable-netbios/

https://www.blackhillsinfosec.com/how-to-disable-llmnr-why-you-want-to/

TCP/IP NETBIOS

Enable Windows Firewall Outbound, all Network Discovery
```
REG add "HKLM\System\CurrentControlSet\Services\WinHttpAutoProxySvc" /v Start /t REG_DWORD /d 4 /f
```

Disable DNS client

```
REG add ... dnscache /v start /t 
```
Force SMB signing: Group policy editor

```
Microsoft network server: Digitally sign communications (always)
```
