#Installation
$ sudo apt-get install snmp snmp-mibs-downloader

#Snmp get
$ snmpget forth 192.168.10.221 .1.3.6.1.4.1.27995.1.3.1.1.1.2.3.1.2.1

#Snmp set integer
$ snmpset -v 2c -c forth 192.168.10.108 .1.3.6.1.4.1.27995.1.3.1.1.1.2.3.1.2.1 i 0

#Snmp set float
$ snmpset -v 2c -c forth 192.168.10.108 <oid> -F 0.5

#Snmp set string
$ snmpset -v 2c -c forth 192.168.10.108 <oid> -s "Hello, world"
