# DONT USE THIS
# THIS METHOD BLOCKED IN IRAN SINCE APRIL 2023
# GFW BLOCK ALL HTTP REQUEST TO OUTSIDE OF IRAN SINCE APRIL 2023



# gfw_resist_http_proxy
knock up gfw active-probe by redirecting it to nginx

- IDEA : hide xray behind nginx
- we build a custom http proxy to manually route traffic to nginx/xray backend
- we log all ip+time+req_header so we clearly observing active-probe IPs !
- it prolong blocking duration but we need more investigation ( we guess some blocking is from pure passive traffic analysis )
- we build our proxy because nginx reverse proxy is not compatible with xray packet , it drop payload of http header (xray http header is not standard)



# protect VPN server against GFW DDOS and port scanning:
- https://github.com/GFW-knocker/GFW-DDOS-protection



# how pyprox work:

- all http request examined , if it xray-valid redirect it to xray(n3) else redirect it to nginx(n2)
- so gfw prober alwayse see nginx and cannot talk to xray directly 
- if all other things ok (dynamic-page website, serve on port 80, other ports closed,...) the prober classifiy you as a legitimate web-server


# Note:
- you can use any port you want (in pyprox ->  my_PORT = 1234  , in terminal -> ufw allow 1234/tcp)
- you can use any domain you want ( Host : blablabla.ir )
- you can use any path just ensure that start and end with slash (set path both in pyprox and in xui-panel)


# the system consist of a these module:<br>
1- custom http proxy to identify xray/v2ray request (port n1=80)<br>
2- nginx and backend web server that mimic a real website (local port n2)<br>
3- xray/v2ray that serve tcp+http protocol on local port  (local port n3)<br>
4- ufw block all ports except port 80 which is open for everyone<br>

# Suggestion:
 - is not guarantee to prevent blocking but it prolong (we still working on!)
 - some blocking is by passive analysis so limit your traffic below 10MB/s or even lower
 - pyprox is a platform to hide xray behind
 - its customizable , you can use it for any path or any other protocol other than Http. 
 - you just need to watch network packet in wireshark and design your own routing decision.
 - you can analyse ip log and block GFW prober IPs in linux firewall

# help
![Alt text](/instruction/pyprox.png?raw=true "pyprox")
![Alt text](/instruction/config0.png?raw=true "config0")
![Alt text](/instruction/config1.png?raw=true "config1")
![Alt text](/instruction/config2.png?raw=true "config2")
![Alt text](/instruction/traffic3.png?raw=true "traffic")

# typical config:

vmess://ew0KICAidiI6ICIyIiwNCiAgInBzIjogInB5cHJveCIsDQogICJhZGQiOiAiMjE2LjIzOS4zOC4xMjAiLA0KICAicG9ydCI6ICI4MCIsDQogICJpZCI6ICIzNDM4NTJjYy1hZDRjLTRiYzMtOTY3Zi1hNDY1YTc3NzYyMzUiLA0KICAiYWlkIjogIjAiLA0KICAic2N5IjogImF1dG8iLA0KICAibmV0IjogInRjcCIsDQogICJ0eXBlIjogImh0dHAiLA0KICAiaG9zdCI6ICJmdHAubW96aWxsYS5vcmciLA0KICAicGF0aCI6ICIvcHViL2ZpcmVmb3gvcmVsZWFzZXMvbGF0ZXN0L3dpbjY0L2VuLVVTL0ZpcmVmb3gtU2V0dXAuZXhlLyIsDQogICJ0bHMiOiAiIiwNCiAgInNuaSI6ICIiLA0KICAiYWxwbiI6ICJodHRwLzEuMSINCn0=


you can set to any url path you want but dont forget to also set path in pyprox and nginx

- path :<br>
  <code>/pub/firefox/releases/latest/win64/en-US/Firefox-Setup.exe/</code><br>

- Request header :<br>
  <code>Host : ftp.mozilla.org</code><br>
  <code>Location : /pub/firefox/releases/latest/win64/en-US/Firefox-Setup.exe/</code><br>
  <code>Referer : http://ftp.mozilla.org/pub/firefox/releases/latest/win64/en-US/</code><br>
  <code>Accept-Language : en-US,en;q=0.9</code><br>
  <code>Content-Type : application/octet-stream</code><br>

- Response header :<br>
  <code>Content-Type : application/octet-stream</code><br>
  <code>Server : nginx</code><br>
  <code>Via : 1.1 google, 1.1 google</code><br>
  <code>Cache-Control : max-age=0</code><br>





# IP Log Analyzer:
- just run the script to analyze all ip log files in IP_Log folder and summary them into a tiny excel sheet
- it list all unique IPs connecting to your server , counting num request to xray/nginx , first seen time , last seen time , percent of malicious probe
- output IP list sorted by percent of malicious probe , which we assume that they are GFW prober
- if some of us publish prober list and aggregate data with each others , we can identify prober IPs with 100% confidence
- so we able to block these IP in firewall (sudo ufw deny from $IP to any)
- obtain ip info from https://www.showmyip.com/ip-whois-lookup/

![Alt text](/instruction/ip_analyze.png?raw=true "ip_analyze")



# run python script:
- set the premission<br>
<code>add   [#!/usr/bin/env python3]     to first line of pyprox.py</code><br>
<code>chmod +x pyprox.py</code><br>
- to run in forground<br>
<code>python pyprox.py</code><br>
- to run in background:<br>
<code>nohup python pyprox.py &</code><br>
- to stop script:<br>
<code>pkill -f pyprox.py</code><br>


# usefull path
<code>/etc/nginx/sites-available/</code><br>
<code>/var/www/html/</code><br>
<code>/var/log/nginx/access.log</code><br>
<code>/etc/x-ui/x-ui.db</code><br>
<code>/usr/local/x-ui/access.log</code><br>
<code>/etc/ufw/</code><br>
<code>/var/log/ufw.log</code><br>

