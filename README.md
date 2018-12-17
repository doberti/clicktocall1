# WEBRTC + SIP Example
This repo contains a simple example of how to build a WebRTC application usign SIP as signaling layer.

The example by no means represents a production-ready application nor presents secure practices. 

The main objective is to show what would be the workflow in a WebRTC app tha uses SIP for signaling.

## Running the example
To run the app, you will need NodeJS and a SIP server. In this example we use Asterisk. 

After cloning the repository, open `js/main.js` and set the `domain` variable to your server address.

Then install the npm dependencies an run the application with npm start. The app will be available at https://localhost:8080

    git clone https://github.com/agilityfeat/webrtc-sip-2.git
    cd webrtc-sip-example
    npm install
    npm start

Open the app in two separate tabs, log in as users `bob` and `lucy`, and click `Call`. Note that an insecure password has been set and you definitely don't want to do this in an production environment.

## SIP Server
For the example to work you need to set up an Asterisk Server. We used version 15.

Configuration files used in this example can be found in the `asterisk-conf` folder.

## Libraries Used

* We use [http-server](https://www.npmjs.com/package/http-server) to serve the files.
* We use [jssip](http://jssip.net/) for sip support
* We use [sdp-interop-sl](https://github.com/StarLeafAPIs/sdp-interop-sl) for making some required adjustments to sdp in chrome




##Configuraciones en asterisk:

### /etc/asterisk/http.conf
'''
[general]
enabled=yes
bindaddr=0.0.0.0
bindport=8088
tlsenable=yes
tlsbindaddr=0.0.0.0:8089
tlscertfile=/etc/asterisk/keys/asterisk.pem
tlsprivatekey=/etc/asterisk/keys/asterisk.key
tlscafile=/etc/asterisk/keys/ca.crt
tlscipher=ALL
tlsclientmethod=ALL
;dtls_verify=no
'''



### /etc/asterisk/pjsip.conf
'''
[transport_wss]
type=transport
bind=0.0.0.0
protocol=wss

[bob]
type=aor
max_contacts=4

[bob]
type=auth
auth_type=userpass
username=bob
password=123456 ; This is an insecure password

[bob]
type=endpoint
context=default
direct_media=no
allow=!all,ulaw,vp8,h264
aors=bob
auth=bob
max_audio_streams=10
max_video_streams=10
webrtc=yes
dtls_cert_file=/etc/asterisk/keys/asterisk.pem
dtls_ca_file=/etc/asterisk/keys/ca.crt
;dtls_verify=no


[dan]
type=aor
max_contacts=4

[dan]
type=auth
auth_type=userpass
username=dan
password=123456 ; This is an insecure password

[dan]
type=endpoint
context=default
direct_media=no
allow=!all,ulaw,vp8,h264
aors=dan
auth=dan
max_audio_streams=10
max_video_streams=10
webrtc=yes
dtls_cert_file=/etc/asterisk/keys/asterisk.pem
dtls_ca_file=/etc/asterisk/keys/ca.crt
;dtls_verify=no


;;; Para estación xlite por ejemplo
[transport-udp-nat]
type=transport
protocol=udp
bind=0.0.0.0
local_net=172.31.24.0/255.255.240.0
external_media_address=18.224.3.22
external_signaling_address=18.224.3.22


[1000]
type=endpoint
context=default
disallow=all
allow=ulaw
allow=alaw
transport=transport-udp-nat
direct_media=no
force_rport=yes
rtp_symmetric=yes
mailboxes=1000@default
auth=1000
aors=1000

[1000]
type=auth
auth_type=userpass
password=1000
username=1000

[1000]
type=aor
max_contacts=10
qualify_frequency=30




;;; Para Vocalcom
[udp-transport]
type=transport
protocol=udp
bind=0.0.0.0

[vdc53]
type=aor
contact=sip:5008@192.168.105.53:5060

[vdc53]
type=endpoint
transport=udp-transport
context=default
disallow=all
allow=alaw
allow=ulaw
aors=vdc53
from_domain=192.168.105.53
direct_media=no

[vdc53]
type=identify
endpoint=vdc53
match=192.168.105.53
'''





### /etc/asterisk/extensions.conf
'''
[default]
exten => bob,1,Dial(PJSIP/${EXTEN})
exten => lucy,1,Dial(PJSIP/${EXTEN})
exten => 1000,1,Dial(PJSIP/${EXTEN})
exten => vdc53,1,Dial(PJSIP/${EXTEN})
exten => dan,1,Dial(PJSIP/${EXTEN})
'''

