# Palo-Alto-XDR-syslog-DHCP-data
Ingest none DHCP data in Palo Alto XDR

The current XDR cannot obtain DHCP data from Syslog or System Data when the firewalls act as DHCP servers. However, if you have a VM-Broker, you can use it to collect Syslog data from the firewall and send it to XDR. From there, you can parse the data into the Microsoft DHCP dataset, and XDR will include it in XDR_DHCP.

Parsing filter for Palo Alto firewall DHCP logs forwareded to Broker VM
[INGEST:vendor="syslog", product="syslog", target_dataset="microsoft_dhcp_raw" , no_hit=drop]
filter  _raw_log  contains "DHCP"; 
 alter
  ip = arrayindex(regextract(_raw_log, "\d+\.\d+\.\d+\.\d+"),0),
  mac = arrayindex(regextract(_raw_log, "[0-9a-f:]{17}"),0),
  hostname = arrayindex(regextract(_raw_log, "hostname (\S+[^ ,])"),0);
 filter hostname != "[unavailable]";
 fields  ip, mac, hostname;

