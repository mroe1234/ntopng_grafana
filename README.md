# ntopng_grafana
This project builds off of ntopng and grafana to provide easy public viewing of flow data

## Dependencies
Obviously this depends on ntopng (which needs to be configured to use influx) and Grafana.  Flux is another big dependency.  InfluxQL has not easy way to reproduce "Top Talkers".  Flux, the new language from the Influxdb team is much more flexible and also easier to use.  It is also a requirement for this dashboard as everything except the world panel (because it doesn't seem to support flux yet) was written in it.
### Grafana Dependencies
Below are a list of plugins I needed to add to Grafana:
 - [InfluxDB (Flux) Datasource [BETA]](https://grafana.com/grafana/plugins/grafana-influxdb-flux-datasource)
 - [JSON](https://github.com/simPod/grafana-json-datasource)
 - [Grafana Bar Gauge](https://grafana.com/docs/grafana/latest/panels/visualizations/bar-gauge-panel/)
 - [World Map Panel](https://github.com/grafana/worldmap-panel)
### Glue
I configured my ntopng instance to use custom disaggregation in order to allow for easy targeting of host populations.  This meant that my ntopng "ifids" or interfaces were not very stable.  As I would add or change filters the ids would change.  To solve, this I used the included cgi one-liner to present to Grafana the list of interfaces in a form that Grafana could use as a variable/dropdown.  I used nginx and fcgiwrapper to glue these pieces together.  Here is my nginx config for reference:
```
location /cgi-bin/ntop {
    		 fastcgi_param SCRIPT_FILENAME  /usr/local/bin/ntop_interfaces;
    		 include fastcgi_params;
    		 fastcgi_pass unix:/var/run/fcgiwrap.socket;
}		 
```
The one-liner relies heavily on jq, you should be able to run the one-liner from the command line to ensure it's working.

## TODO
This is an early release and there are still issues.  The big todo is utilize the 1-day rollups ntopng creates in influx for better performance.  I would also like to fix the worldmap to use flux, however that waits on the worldmap people.  I welcome any other fixes or suggestions.

![Top Talkers](https://github.com/mroe1234/ntopng_grafana/blob/master/images/top_talkers.png?raw=true)
![Destination Port](https://github.com/mroe1234/ntopng_grafana/blob/master/images/destport.png?raw=true)
![Host Threat Score](https://github.com/mroe1234/ntopng_grafana/blob/master/images/host_threat_score.png?raw=true)
![Layer 4 breakdown](https://github.com/mroe1234/ntopng_grafana/blob/master/images/layer4.png?raw=true)
![World Map](https://github.com/mroe1234/ntopng_grafana/blob/master/images/worldmap.png?raw=true)
