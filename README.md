# Dnsmasq SNIproxy One-click Install

### Script description:

* Brief description of the principle: Use the DNS of [Dnsmasq](http://thekelleys.org.uk/dnsmasq/doc.html) to hijack website resolution to [SNIproxy](https://github.com/dlundquist/sniproxy) reverse to the agent's page.

* Purpose: Allow VPS that cannot watch streaming media to watch (prerequisite: one of the VPS must be able to watch streaming media).

* Features: The script unlocks `Netflix Hulu HBO`[etc.](https://github.com/JebonRX/bypassdemen/blob/main/proxy-domains.txt) by default. If you need to add or delete streaming domain names, please edit the file`/etc /dnsmasq.d/custom_netflix.conf` and `/etc/sniproxy.conf`

* Script support system: CentOS6+, Debian8+, Ubuntu16+
     * Theoretically supports the above systems and does not limit virtualization types. If you have any questions, please provide feedback.
     * If the IP displayed at the end of the script does not match the actual public IP, please modify the IP address in the file `/etc/sniproxy.conf`

### Script usage:

     bash dnsmasq_sniproxy.sh [-h] [-i] [-f] [-id] [-fd] [-is] [-fs] [-u] [-ud] [-us]
       -h , --help Display Help Information
       -i , --install Install Dnsmasq + SNI Proxy
       -f , --fastinstall Quickly install Dnsmasq + SNI Proxy
       -id, --installdnsmasq Install only Dnsmasq
       -fd, --installdnsmasq Quickly install Dnsmasq
       -is, --installsniproxy Install only SNI Proxy
       -fs, --fastinstallsniproxy Quickly install SNI Proxy
       -u , --uninstall Uninstall Dnsmasq + SNI Proxy
       -ud, --undnsmasq Uninstall Dnsmasq
       -us, --unsniproxy Uninstall SNI Proxy

### Quick installation (recommended):
```Bash
wget --no-check-certificate -O dnsmasq_sniproxy.sh https://raw.githubusercontent.com/JebonRX/bypassdemen/main/dnsmasq_sniproxy.sh && bash dnsmasq_sniproxy.sh -f
```

### Normal installation:
```Bash
wget --no-check-certificate -O dnsmasq_sniproxy.sh https://raw.githubusercontent.com/JebonRX/bypassdemen/main/dnsmasq_sniproxy.sh && bash dnsmasq_sniproxy.sh -i
```

### Uninstall method:
```Bash
wget --no-check-certificate -O dnsmasq_sniproxy.sh https://raw.githubusercontent.com/JebonRX/bypassdemen/main/dnsmasq_sniproxy.sh && bash dnsmasq_sniproxy.sh -u
```

### Instructions:
Just change the DNS address of the proxy host to the host IP where dnsmasq is installed. If it is not available, try to keep only one DNS address in the configuration file.

To prevent abuse, it is recommended not to disclose the IP address and use a firewall to restrict it.

### Debugging and troubleshooting:
- Confirm that sniproxy is running effectively

   View sniproxy status: `systemctl status sniproxy`

   If sniproxy is not running, check whether there are other services occupying ports 80 and 443, causing port conflicts. Check the port monitoring command: `netstat -tlunp | grep 443`

- Confirm that the firewall allows 53,80,443

   Debugging can directly turn off the firewall `systemctl stop firewalld.service`

   The security group ports of operators such as Alibaba Cloud/Google Cloud/AWS also need to be released.
   Can be tested through other servers `telnet 1.2.3.4 53`

- Domain name resolution test

   After trying to configure dns with other servers, resolve the domain name: nslookup netflix.com to determine whether the IP is the NETFLIX proxy machine IP
   If the nslookup command does not exist, centos installation: `yum install -y bind-utils` ubuntu&debian installation: `apt-get -y install dnsutils`

- Solution to systemd-resolve service occupying port 53
   Use `netstat -tlunp|grep 53` to find that port 53 is occupied by systemd-resolved
   Modify `/etc/systemd/resolved.conf`
   ```
   [Resolve]
   DNS=8.8.8.8 1.1.1.1 #Uncomment and add dns
   #FallbackDNS=
   #Domains=
   #LLMNR=no
   #MulticastDNS=no
   #DNSSEC=no
   #Cache=yes
   DNSStubListener=no #Uncomment and change yes to no
   ```
   Then execute the following command and restart systemd-resolved
   ```
   ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
   systemctl restart systemd-resolved.service
   ```

---

___This script is only used to unlock streaming media___
