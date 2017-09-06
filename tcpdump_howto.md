
# TCPDUMP  
คือโปรแกรมดักจับข้อมูลที่วิ่งผ่าน(เข้า/ออก)พอร์ตแลน(dump traffic on a network interface) ของเครื่องหรืออุปกรณ์ที่ใช้งาน  
การดักจับที่ network interface ใดๆจำเป็นจะต้องมีสถานะเป็น up อยู่ 
และโปรแกรมนี้จำเป็นจะต้องรันด้วยสิทธิของ root ไม่เช่นนั้นจะไม่สามารถมองเห็น packet ใดๆได้  
  
# วิธีใช้งานเบื้องต้น  
  
ถ้าไม่ระบุออปชั่นใดๆเลย tcpdump จะเลือกดักจับ packet ที่เข้าออก อินเตอร์เฟสแลนพอร์ตแรก   
  
```
$ sudo tcpdump
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
14:12:44.857782 IP 192.168.10.85.ssh > 192.168.10.82.3976: Flags [P.], seq 3919202162:3919202374, ack 2006053698, win 251, length 212
14:12:44.858531 IP 192.168.10.82.3976 > 192.168.10.85.ssh: Flags [.], ack 0, win 16098, length 0
14:12:44.858575 IP 192.168.10.85.46596 > 192.168.10.1.domain: 8568+ PTR? 82.10.168.192.in-addr.arpa. (44)
14:12:44.861353 IP 192.168.10.1.domain > 192.168.10.85.46596: 8568 NXDomain 0/1/0 (103)
14:12:44.861699 IP 192.168.10.85.52960 > 192.168.10.1.domain: 18272+ PTR? 85.10.168.192.in-addr.arpa. (44)
...
^C
1565 packets captured
1608 packets received by filter
40 packets dropped by kernel
```
  
ระบุพอร์ตที่จะดักจับด้วยออปชัน `-i`    
  
```
$ sudo tcpdump -i eth0
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
13:33:31.972223 IP 192.168.10.85.ssh > 192.168.10.82.3976: Flags [P.], seq 3907782010:390778
13:33:31.987615 IP 192.168.10.85.42532 > 192.168.10.1.domain: 12753+ PTR? 82.10.168.192.in-a
13:33:31.990097 IP 192.168.10.1.domain > 192.168.10.85.42532: 12753 NXDomain 0/1/0 (103)
13:33:31.990440 IP 192.168.10.85.33641 > 192.168.10.1.domain: 19102+ PTR? 85.10.168.192.in-a
13:33:31.992502 IP 192.168.10.1.domain > 192.168.10.85.33641: 19102 NXDomain 0/1/0 (103)
13:33:31.993249 IP 192.168.10.85.53595 > 192.168.10.1.domain: 18700+ PTR? 1.10.168.192.in-ad
13:33:31.993763 IP 192.168.10.85.ssh > 192.168.10.82.3976: Flags [P.], seq 212:408, ack 1, 
...
^C
1159 packets captured
1197 packets received by filter
33 packets dropped by kernel
```
  
โดย default แล้ว tcpdump จะทำการแปลงหมายเลข ip ให้เป็นชื่อ hostname (resolve ip to hostname) 
ซึ่งจะกินทรัพยากรของระบบอย่างมากในกรณีที่ traffic มีปริมาณมาก 
เราสามารถปิดคุณสมบัติดังกล่าวได้ด้วยการเพิ่มออปชั่น `-n`   
  
หากใช้งานร่วมกับการ redirection output ไปที่อื่น
ควรเพิ่มออปชั่น `-l` เพื่อให้ stdout ออกไปทีละบรรทัดด้วย เช่น  
`sudo tcpdump -i eth0 -n -l > dat & tail -f dat`  
   
```
$ sudo tcpdump -i eth0 -n -l > capture.log 
^C
$ cat capture.log 
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
14:30:56.811691 IP 192.168.10.85.22 > 192.168.10.82.3976: Flags [P.], seq 3919504250:3919504462, ack 2006060150, win 251, length 212
14:30:56.812067 IP 192.168.10.85.22 > 192.168.10.82.3976: Flags [P.], seq 212:408, ack 1, win 251, length 196
14:30:56.812159 IP 192.168.10.82.3976 > 192.168.10.85.22: Flags [.], ack 212, win 16425, length 0
14:30:56.812293 IP 192.168.10.85.22 > 192.168.10.82.3976: Flags [P.], seq 408:668, ack 1, win 251, length 260
14:30:56.812470 IP 192.168.10.85.22 > 192.168.10.82.3976: Flags [P.], seq 668:832, ack 1, win 251, length 164
...
14:30:57.131006 IP 192.168.10.82.3976 > 192.168.10.85.22: Flags [P.], seq 53:105, ack 263460, win 16197, length 52
14:30:57.131238 IP 192.168.10.85.22 > 192.168.10.82.3976: Flags [P.], seq 263736:263916, ack 105, win 251, length 180
1740 packets captured
1753 packets received by filter
```
  
หากต้องการแค่ข้อมูล ip address และ port ให้ใช้ `-q`  
  
```
$ sudo tcpdump -i eth0 -n -l -q
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
14:35:54.685101 IP 192.168.10.85.22 > 192.168.10.82.3976: tcp 212
14:35:54.685443 IP 192.168.10.85.22 > 192.168.10.82.3976: tcp 132
14:35:54.685667 IP 192.168.10.85.22 > 192.168.10.82.3976: tcp 132
14:35:54.685820 IP 192.168.10.82.3976 > 192.168.10.85.22: tcp 0
14:35:54.685844 IP 192.168.10.85.22 > 192.168.10.82.3976: tcp 132
...
14:35:55.139327 IP 192.168.10.85.22 > 192.168.10.82.3976: tcp 196
14:35:55.139824 IP 192.168.10.85.22 > 192.168.10.82.3976: tcp 132
14:35:55.139959 IP 192.168.10.82.3976 > 192.168.10.85.22: tcp 0
14:35:55.140053 IP 192.168.10.82.3976 > 192.168.10.85.22: tcp 52
^C
2536 packets captured
2542 packets received by filter
1 packet dropped by kernel
```
  
หากต้องการข้อมูล mac address(layer 2) ให้ระบุออปชั่น `-e`  
  
```
sudo tcpdump -i eth0 -nn -l -q -e
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
14:43:30.124256 08:00:27:a6:01:ec > 14:fe:b5:9f:cf:15, IPv4, length 266: 192.168.10.85.22 > 192.168.10.82.3976: tcp 212
14:43:30.124901 14:fe:b5:9f:cf:15 > 08:00:27:a6:01:ec, IPv4, length 60: 192.168.10.82.3976 > 192.168.10.85.22: tcp 0
14:43:30.124934 08:00:27:a6:01:ec > 14:fe:b5:9f:cf:15, IPv4, length 234: 192.168.10.85.22 > 192.168.10.82.3976: tcp 180
14:43:30.125256 08:00:27:a6:01:ec > 14:fe:b5:9f:cf:15, IPv4, length 346: 192.168.10.85.22 > 192.168.10.82.3976: tcp 292
...
14:43:30.413845 08:00:27:a6:01:ec > 14:fe:b5:9f:cf:15, IPv4, length 234: 192.168.10.85.22 > 192.168.10.82.3976: tcp 180
14:43:30.413942 14:fe:b5:9f:cf:15 > 08:00:27:a6:01:ec, IPv4, length 60: 192.168.10.82.3976 > 192.168.10.85.22: tcp 0
14:43:30.414338 08:00:27:a6:01:ec > 14:fe:b5:9f:cf:15, IPv4, length 346: 192.168.10.85.22 > 192.168.10.82.3976: tcp 292
14:43:30.414533 14:fe:b5:9f:cf:15 > 08:00:27:a6:01:ec, IPv4, length 106: 192.168.10.82.3976 > 192.168.10.85.22: tcp 52
^C
1702 packets captured
1711 packets received by filter
6 packets dropped by kernel
```

สำหรับการ save ข้อมูลที่ดักจับได้ให้อยู่ใน format `.pcap` (same as `.cap`, `.dmp`, `Tcpdump`) 
ให้ใช้ออปชั่น `-w filename`  
ไฟล์ดังกล่าวจะสามารถเปิดกับโปรแกรม Wireshark / Ethereal ได้  
  
```
$ sudo tcpdump -i eth0 -w capture-rawdat.pcap
^C
$ ls *.pcap
capture-rawdat.pcap
```
  
การอ่านข้อมูล `.pcap` ด้วยโปรแกรม tcpdump ให้ใช้ออปชั่น `-r xxx.pcap` ดังนี้    
  
```
sudo tcpdump -r capture-rawdat.pcap -l -n
14:49:34.099341 ARP, Request who-has 192.168.10.13 tell 192.168.10.66, length 46
14:49:34.099430 ARP, Request who-has 192.168.10.64 tell 192.168.10.66, length 46
...
14:49:34.284743 IP 192.168.10.118.1900 > 239.255.255.250.1900: UDP, length 330
14:49:34.284850 IP 192.168.10.118.1900 > 239.255.255.250.1900: UDP, length 328
14:49:34.285080 IP 192.168.10.141.63856 > 239.255.255.250.1900: UDP, length 174
```
   
   
   
