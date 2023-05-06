# Problem
- Slow Wireguard Connection in Linux (Arch Linux)

# Setup
- Arch Linux (Kernel: 6.3.1-arch1-1)
- Surfshark Wireguard VPN (Manual Connection)
- Router: Asus RT-AC68U (Firmware: Asuswrt-Merlin Asus386.10)
- Router is NAT only. Neither Wireguard Endpoint nor Client is at the Router

# Symptons
## Arch Linux - Manual connection
- First connection is normal (800M DL / 500M UP)
- Disconnect and reconnect => Slow throughput in Arch Linux (30M DL/ 1.5M UP)
- Go to router page http://192.168.1.1/Advanced_WAN_Content.asp, change nothing and click "Apply" => Speed returns to normal (One time fix only)
- Go to router page http://192.168.1.1/Advanced_SwitchCtrl_Content.asp => Change "NAT Acceleration" from "Auto" to "Disable" => Speed returns to normal (But ~300M UP/DL due to lack of HW acceleration)

## Windows 10
- Cannot reproduce the problem using official client

# Workaround
- Enable SSH
- Crontab -e
- Add the following to workaround the NAT acceleration problem? (Check and Reset the conntrack table of the Wireguard connection if more than 2 recordsevery 15 seconds, replace 192.168.1.10 to your computer static ip)
```
* * * * * ( sleep  0 ; if [ $(conntrack -L -p udp -s 192.168.1.10 --dport 51820 | wc -l) -gt "1" ]; then conntrack -D conntrack -p udp -s 192.168.1.10 --dport 51820; fi )
* * * * * ( sleep 15 ; if [ $(conntrack -L -p udp -s 192.168.1.10 --dport 51820 | wc -l) -gt "1" ]; then conntrack -D conntrack -p udp -s 192.168.1.10 --dport 51820; fi )
* * * * * ( sleep 30 ; if [ $(conntrack -L -p udp -s 192.168.1.10 --dport 51820 | wc -l) -gt "1" ]; then conntrack -D conntrack -p udp -s 192.168.1.10 --dport 51820; fi )
* * * * * ( sleep 45 ; if [ $(conntrack -L -p udp -s 192.168.1.10 --dport 51820 | wc -l) -gt "1" ]; then conntrack -D conntrack -p udp -s 192.168.1.10 --dport 51820; fi )
```
- Disable SSH

# Disclaimer
I will not responsible for any damage or loss of data that may occur as a result of following these instructions
