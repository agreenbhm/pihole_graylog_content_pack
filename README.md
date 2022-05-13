# Graylog3 supported
``Content Pack for PiHole 5 with Graylog``  
  
Built and based off of https://github.com/chateaulav/pihole. Includes setting GeoIP, so ensure you download the current City db from Maxmind, and install the current Threat intelligence content packs. A seperate input is established to collect only pihole syslog traffic.

## Syslog requirements
**syslog-ng** *Best option, simple and only sends pihole data*
```
#apt install syslog-ng -y
#vi /etc/syslog-ng/conf.d/10-pihole.conf
source s_pihole_log { file("/var/log/pihole.log"); };
destination d_graylog {udp("server.ip" port(1515)); };
log { source(s_pihole_log); destination(d_graylog); };
```


### Content Pack includes:

* **Input**:  
``Sets a seperate input for just pihole DNS Logs``
  * piHole Syslog (``Listen 0.0.0.0:1515/UDP``)
  
* **Extractor**:
  * app_name (Looking for ``pihole``)

* **Pipeline**:  
``Creates Multiple fields to enable stronger queries and analytics``
  * -2 Rules:
    * ``drop pihole unnecessary messages``
  * -1 Rules:
    * ``dnsmasq pihole split``
  * 0 Rules:
    * ``PiHole GeoIP Set``
  * 1 Rules:
    * ``threatintel (dnsmasq)``
  * 2 Rules:
    * ``threatintel (2) inflate``
  
* **Lookup Table\Cache**:
  * geolite2-city (``/usr/share/graylog/data/GeoLite2-City.mmdb``)
  
* **Dashboard called ``DNS Intel``**:
  * DNS Location Requested IP (from answers)
  * DNS Activities (24h)
  * Thread Names (24h)
  * Blocked Domains (24h)
  * Blackholed Requests (24h)
  * Threat Indicated (24h)
  * DNS Clients (24h)
  * DNS Querys (24h)
  * Owning Companies (24h)
