just one more copy of respbery pi AP but added  address=/#/192.168.2.1 @dnsmasq.conf

#<b>update</b><br/>
sudo apt-get update <br/>
sudo apt-get upgrade<br/>
<br/><br/>
#<b>install the Sevice:</b><br/>

sudo apt-get install dnsmasq hostapd<br/>

#<b>stop them</b><br>
sudo systemctl stop dnsmasq<br>
sudo systemctl stop hostapd<br/>

#<b>Configuring a static IP:</b><br/>
sudo nano /etc/dhcpcd.conf<br/>

#<b>Go to the end of the file and edit it so that it looks like the following:</b><br/>
interface wlan0<br/>
    static ip_address=192.168.4.1/24

<b>Now restart the dhcpcd daemon and set up the new `wlan0` configuration:</b><br/>
sudo service dhcpcd restart</br>

<b>Configuring the DHCP server (dnsmasq)</b><br/>
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig<br/>
sudo nano /etc/dnsmasq.conf<br>

<b>Type or copy the following information into the dnsmasq configuration file and save it:</b><br/>

interface=wlan0<br/>
  dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24<br/>
  address=/#/192.168.2.1<br/>  
  
  <b>"address=/#/192.168.2.1" will redirect all DNS requests to 192.168.2.1 - pi router </b><br/>
  <br/>
  <br/>
  <b>Configuring the access point host software (hostapd)</b><br/>
  sudo nano /etc/hostapd/hostapd.conf<br/>
  
interface=wlan0<br/>
driver=nl80211<br/>
ssid=NameOfNetwork<br/>
hw_mode=b<br/>
channel=7<br/>
wmm_enabled=0<br/>
macaddr_acl=0<br/>
auth_algs=1<br/>
ignore_broadcast_ssid=0<br/>
wpa=2<br/>
wpa_passphrase=password<br/>
wpa_key_mgmt=WPA-PSK<br/>
wpa_pairwise=TKIP<br/>
rsn_pairwise=CCMP<br/>

<b>We now need to tell the system where to find this configuration file.</b><br/>
sudo nano /etc/default/hostapd<br/>

<b>Find the line with #DAEMON_CONF, and replace it with this:</b><br/>
DAEMON_CONF="/etc/hostapd/hostapd.conf"<br/>

<b>Start it up</b><br/>
sudo service hostapd start  <br/>
sudo service dnsmasq start  <br/>

<b>Add routing and masquerade</b><br/>
Edit /etc/sysctl.conf and uncomment this line:<br/>
net.ipv4.ip_forward=1<br/>

<b>Add a masquerade for outbound traffic on eth0:</b><br/>
sudo iptables -t nat -A  POSTROUTING -o eth0 -j MASQUERADE<br/>

<b>Save the iptables rule.</b><br/>
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"<br/>

Edit /etc/rc.local and add this just above "exit 0" to install these rules on boot.<br/>
iptables-restore < /etc/iptables.ipv4.nat<br/>

sudo Reboot<br/><br/>

<b>now you can connect to your pi via wifi</b><br/>

<b> install nodeJS<b><br/>
curl -sL https://rpm.nodesource.com/setup_11.x | bash -<br/>
# Using Ubuntu<br/>
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -<br/>
sudo apt-get install -y nodejs<br/>

# Using Debian, as root<br/>
curl -sL https://deb.nodesource.com/setup_11.x | bash -<br/>
apt-get install -y nodejs<br/>
<br/>
apt-get install npm <br/>
<br/>
update npm with npm<br/>


mkdir node<br/>
cd node<br/>
nano hello.js<br/>
<B>paste that to there:</b><br/>
var http = require('http');<br/>
<br/>
http.createServer(function (req, res) {<br/>
  res.writeHead(200, {'Content-Type': 'text/plain'});<br/>
  res.end('Hello World!');<br/>
}).listen(8080); <br/>


<b>connet to wifi and open boweser or other app  at 192.168.4.1:8080 and see that hello msg on screen</b>



