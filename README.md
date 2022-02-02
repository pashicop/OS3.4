# OS3.4
# 1
Создал unit файл `/etc/systemd/system/node_exporter.service`, создал пользователя, от которого будет запускаться сервис, добавил в автозагрузку с помощью `systemctl enable node_exporter` и добавил `EnvironmentFile=/home/vagrant/args` в который занёс аргумент `--web.listen-address=":9101"`, запускающий сервис на порту 9101. Всё работает! Ниже листинг  
```
vagrant@vagrant:~$ cat args
one=--web.listen-address=":9101"
vagrant@vagrant:~$ cat /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
EnvironmentFile=/home/vagrant/args
User=node_exporter
Group=node_exporter
ExecStart=/usr/local/bin/node_exporter $one

[Install]
WantedBy=default.target
vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-02-01 18:31:37 UTC; 1min 2s ago
   Main PID: 2153 (node_exporter)
      Tasks: 5 (limit: 1710)
     Memory: 5.5M
     CGroup: /system.slice/node_exporter.service
             └─2153 /usr/local/bin/node_exporter --web.listen-address=:9101

Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=thermal_zone
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=time
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=timex
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=udp_queues
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=uname
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=vmstat
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=xfs
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:115 level=info collector=zfs
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.453Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9101
Feb 01 18:31:37 vagrant node_exporter[2153]: ts=2022-02-01T18:31:37.454Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
vagrant@vagrant:~$ sudo systemctl stop node_exporter
vagrant@vagrant:~$ sudo systemctl start node_exporter
vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-02-01 18:32:50 UTC; 2s ago
   Main PID: 2201 (node_exporter)
      Tasks: 6 (limit: 1710)
     Memory: 2.5M
     CGroup: /system.slice/node_exporter.service
             └─2201 /usr/local/bin/node_exporter --web.listen-address=:9101

Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=thermal_zone
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=time
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=timex
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=udp_queues
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=uname
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=vmstat
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=xfs
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:115 level=info collector=zfs
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.487Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9101
Feb 01 18:32:50 vagrant node_exporter[2201]: ts=2022-02-01T18:32:50.488Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
vagrant@vagrant:~$ curl 'localhost:9101/metrics'|tail -n 10
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 61969    0 61969    0     0  2161k      0 --:--:-- --:--:-- --:--:-- 2161k
promhttp_metric_handler_errors_total{cause="encoding"} 0
promhttp_metric_handler_errors_total{cause="gathering"} 0
# HELP promhttp_metric_handler_requests_in_flight Current number of scrapes being served.
# TYPE promhttp_metric_handler_requests_in_flight gauge
promhttp_metric_handler_requests_in_flight 1
# HELP promhttp_metric_handler_requests_total Total number of scrapes by HTTP status code.
# TYPE promhttp_metric_handler_requests_total counter
promhttp_metric_handler_requests_total{code="200"} 0
promhttp_metric_handler_requests_total{code="500"} 0
promhttp_metric_handler_requests_total{code="503"} 0
vagrant@vagrant:~$
```
# 2
