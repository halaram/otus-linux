
В сценарии ansible настроены права и изменён контекст selinux на named_zone_t каталога /etc/named.  
Настроен split dns для client и client2.  
Добавлен дополнительный ключ zonetransfer2.key для трансфера зоны dns.lab, которая различается в разных view.  

Для проверки также применялась утилита nslookup, так как она имеет компактный вывод.  

### Проверка client (видит обе зоны, но в зоне dns.lab только web1)


    [root@client ~]# dig @192.168.50.10 web1.dns.lab

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-9.P2.el7 <<>> @192.168.50.10 web1.dns.lab
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7320
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 3

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;web1.dns.lab.			IN	A

    ;; ANSWER SECTION:
    web1.dns.lab.		3600	IN	A	192.168.50.15

    ;; AUTHORITY SECTION:
    dns.lab.		3600	IN	NS	ns02.dns.lab.
    dns.lab.		3600	IN	NS	ns01.dns.lab.

    ;; ADDITIONAL SECTION:
    ns01.dns.lab.		3600	IN	A	192.168.50.10
    ns02.dns.lab.		3600	IN	A	192.168.50.11

    ;; Query time: 0 msec
    ;; SERVER: 192.168.50.10#53(192.168.50.10)
    ;; WHEN: Wed Nov 13 17:16:59 UTC 2019
    ;; MSG SIZE  rcvd: 127



    [root@client ~]# nslookup web2.dns.lab 192.168.50.10
    Server:		192.168.50.10
    Address:		192.168.50.10#53

    ** server can't find web2.dns.lab: NXDOMAIN



    [root@client ~]# nslookup www.newdns.lab 192.168.50.11
    Server:		192.168.50.11
    Address:	192.168.50.11#53

    Name:	www.newdns.lab
    Address: 192.168.50.20
    Name:	www.newdns.lab
    Address: 192.168.50.15


### Проверка client2 (видит только dns.lab)

    [root@client2 ~]# dig @192.168.50.10 web1.dns.lab

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-9.P2.el7 <<>> @192.168.50.10 web1.dns.lab
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 22764
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 3

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;web1.dns.lab.			IN	A

    ;; ANSWER SECTION:
    web1.dns.lab.		3600	IN	A	192.168.50.15

    ;; AUTHORITY SECTION:
    dns.lab.		3600	IN	NS	ns02.dns.lab.
    dns.lab.		3600	IN	NS	ns01.dns.lab.

    ;; ADDITIONAL SECTION:
    ns01.dns.lab.		3600	IN	A	192.168.50.10
    ns02.dns.lab.		3600	IN	A	192.168.50.11

    ;; Query time: 0 msec
    ;; SERVER: 192.168.50.10#53(192.168.50.10)
    ;; WHEN: Wed Nov 13 17:29:06 UTC 2019
    ;; MSG SIZE  rcvd: 127



    [root@client2 ~]# nslookup web2.dns.lab 192.168.50.11
    Server:		192.168.50.11
    Address:	192.168.50.11#53

    Name:	web2.dns.lab
    Address: 192.168.50.20



    [root@client2 ~]# nslookup www.newdns.lab 192.168.50.10
    Server:		192.168.50.10
    Address:	192.168.50.10#53

    ** server can't find www.newdns.lab: NXDOMAIN
