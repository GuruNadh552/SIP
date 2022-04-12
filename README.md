# ASTERISK SERVER CONFIGURATION

### SYSTEM SETUP

Asterisk and SIP.js were tested using the following setup:

- Ubuntu 
- Asterisk 19
- OpenSSL

### Required Packages

Install the following dependencies:

- wget
- gcc
- gcc-c++
- ncurses-devel
- libxml2-devel
- sqlite-devel
- libsrtp-devel
- libuuid-devel
- openssl-devel

##### Installing Dependenices 
# 

```sh
sudo apt-get install wget gcc gcc-c++ ncurses-devel libuuid-devel jansson-devel libxml2-devel sqlite-devel libsrtp-devel openssl-devel
```

## Install Asterisk
1. cd `/usr/local/src/.`
2. Download Asterisk with `wget https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-16.9.0.tar.gz.`
3. Extract Asterisk: `tar zxvf asterisk*.`
4. Enter the Asterisk directory: `cd /usr/local/src/asterisk*.`
5. Run the Asterisk configure script: `./configure --with-jansson-bundled.`
6. Run the Asterisk menuselect tool: `make menuselect.`
7. In the menuselect, go to the resources option and ensure that res_srtp and pjproject is enabled. If there are 3 x’s next to res_srtp, there is a problem with the srtp library and you must reinstall it. Save the configuration (press x).
8. Compile and install Asterisk: `make && make install`.
9. If you need the sample configs you can run `make samples` to install the sample configs. If you need to install the Asterisk startup script you can run `make config`.


## Self Signed Certificate
1. `mkdir /etc/asterisk/keys`
2. Enter the Asterisk scripts directory: `cd /usr/local/src/asterisk*/contrib/scripts.`
3. Create the DTLS certificates: `./ast_tls_cert -C localhost -O "RGUKT SKLM" -d /etc/asterisk/keys.`
4. Changing the permissions of the folder `/etc/asterisk/keys`
5. `chown - R root: /etc/asterisk/keys`

## Configure Asterisk
By default, Asterisk config files are located in `/etc/asterisk/`. Start by editing http.conf and make sure that the following lines are uncommented:
```sh
;http.conf
[general]
enabled=yes
bindaddr=127.0.0.1 ;
bindport=8088 ;
tlsenable=yes
tlsbindaddr=127.0.0.1:8089 ;
tlscertfile=/etc/asterisk/keys/asterisk.pem
```
extensions.conf
```
[internal]
exten => 7001,1,Answer()
exten => 7001,2,Dial(SIP/7001,60)
exten => 7001,3,Playback(vm-nobodyavail)
exten => 7001,4,VoiceMail(7001@main)
exten => 7001,5,Hangup()

exten => 7002,1,Answer()
exten => 7002,2,Dial(SIP/7002,60)
exten => 7002,3,Playback(vm-nobodyavail)
exten => 7002,4,VoiceMail(7002@main)
exten => 7002,5,Hangup()

exten => 8001,1,VoicemailMain(7001@main)
exten => 8001,2,Hangup()

exten => 8002,1,VoicemailMain(7002@main)
exten => 8002,2,Hangup()
```
sip.conf
```
[general]
context=internal
allowguest=no
allowoverlap=no
bindport=5060
bindaddr=0.0.0.0
srvlookup=no
disallow=all
allow=ulaw
alwaysauthreject=yes
canreinvite=no
nat=yes
session-timers=refuse
localnet=192.168.1.0/255.255.255.0
externip=18.188.229.244

[7001]
type=friend
host=dynamic
secret=123
context=internal

[7002]
type=friend
host=dynamic
secret=456
context=internal
```
voicemail.conf
```
[main]
7001 => 123
7002 => 456
```



To start the server
```
asterisk -vvvr
```
Logs will be displayed in the Terminal.






