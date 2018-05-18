# zap_pi
Configuration files to turn a Raspberry Pi Zero-W into  wireless access point and client and act as a console server for a network device.

Requirements:
Raspberry Pi Zero-W
https://www.raspberrypi.org/products/raspberry-pi-zero-w/  

Raspberry Pi Zero W; with lego and Boba Fett for scale:  


Micro usb cable with power converter
https://www.amazon.com/Kasstino-Female-Micro-Adapter-Tablet/dp/B01ICSIZ3U/ref=sr_1_11?s=pc&ie=UTF8&qid=1525978767&sr=1-11&keywords=micro+usb+male+to+usb+converter




1.  `sudo apt-get update`
2.  `sudo apt-get upgrade`
3.  `sudo apt-get install dnsmasq hostapd screen`

4.  Edit `/etc/rc.local` and add the following ABOVE `exit 0`

```
service hostapd stop
service dnsmasq start
iw dev wlan0 interface add uap0 type __ap
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
ifdown wlan0
ip link set uap0 up
ip addr add 192.168.99.1/24 broadcast 192.168.99.255 dev uap0
service hostapd start
ifup wlan0
service dnsmasq start
```

5.  Edit the `/etc/dnsmasq` file and add and/or adjust the following for your AP's DHCP network scope:
```
interface=uap0
dhcp-range=192.168.4.20,192.168.4.100,255.255.255.0,12h
```

6.  Edit your `/etc/hostapd/hostapd.conf` file and change your SSID and passphrase appropriately.
```
interface=uap0
driver=nl80211
ssid=YOUR-SSID
hw_mode=g
channel=7
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=YOUR-PASSPHRASE
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

7.  Edit `/etc/default/hostapd and add this line:
`DAEMON_CONF="/etc/hostapd/hostapd.conf"'

8.  If you intend to use an attached ethernet usb dongle or the Pi Zero as a wireless client WHILE it is an AP, you will need to allow for routing by editing `/etc/sysctl.conf` and uncommenting:
`net.ipv4.ip_forward=1`  

9.  Reboot


10.  Connect the Zero to the network device via USB.  

11.  Login to the Zero remotely and run `screen /dev/ttyUSB0`

12.  `CTRL-A` then `K` will break out and kill the "screen"  

