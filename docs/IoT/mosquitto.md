On a previous post I described how to make mosquitto debian packages. This turned out to be a bit problematic, so I’ve now put up an experimental debian repository for mosquitto. It includes packages for the i386, amd64, armel and raspberry pi (raspbian armhf ) architectures.

It’s worth repeating that this is experimental – there are package changes that haven’t been vetted by a Debian developer so it’s possible something will break. I’ve tested myself and had no problems so far.

To use the new repository you should first import the repository package signing key:

wget http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key
sudo apt-key add mosquitto-repo.gpg.key

Then make the repository available to apt:

cd /etc/apt/sources.list.d/

 Then one of the following, depending on which version of debian you are using: 
sudo wget http://repo.mosquitto.org/debian/mosquitto-wheezy.list
sudo wget http://repo.mosquitto.org/debian/mosquitto-jessie.list



 

Then update apt information:

apt-get update

And discover what mosquitto packages are available:

apt-cache search mosquitto

Or just install:

apt-get install mosquitto


Start with a fresh new Pi (B+):
◾Put the latest Raspbian image on an SD
◾Raspi-config: expand filesystem, enable SPI, set timezone, change password
◾Optional: set up wifi, static IP, and reboot
◾Run rpi-update, apt-get update, apt-get upgrade
◾Reboot and switch to using terminal (instead of directly connected screen & keyboard)

Install Mosquitto:

First get the repository package signing key, import it into apt, and clean up the key file again:
wget http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key
sudo apt-key add mosquitto-repo.gpg.key
rm mosquitto-repo.gpg.key


Then make the mosquitto repository available to apt:
cd /etc/apt/sources.list.d/
sudo wget http://repo.mosquitto.org/debian/mosquitto-repo.list
sudo apt-get update


Install the core packages (server & clients):
sudo apt-get install mosquitto mosquitto-clients


Test Mosquitto:

Terminal window 1:
mosquitto_sub -d -t hello/world


Terminal window 2:
mosquitto_pub -d -t hello/world -m "Greetings from Terminal window 2"


Troubleshooting:

If nothing happens on either terminal for a few minutes, and then both show:
Error: Connection timed out


You should check your /etc/network/interfaces file. If you set up your wifi by replacing the "auto lo" line with "auto wlan0" MQTT won't work.

Change it to something like this instead:
auto lo
iface lo inet loopback

allow-hotplug wlan0
auto wlan0
etc...


If you're not sure if Mosquitto is actually running and listening on port 1883 (the default), you can use this command:
ps -ef | grep mosq && netstat -tln | grep 1883


It should show something like this:
107       2273     1  0 Jan22 ?        00:01:03 /usr/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf
pi       17010 16806  0 07:16 pts/0    00:00:00 grep --color=auto mosq
tcp        0      0 0.0.0.0:1883            0.0.0.0:*               LISTEN

