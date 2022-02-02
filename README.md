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


### По процессору можно посмотреть загрузку
```
node_cpu_seconds_total{cpu="0",mode="idle"} 42370.13
..
node_cpu_seconds_total{cpu="0",mode="system"} 11.87
node_cpu_seconds_total{cpu="0",mode="user"} 4.88
node_cpu_seconds_total{cpu="1",mode="idle"} 42381.57
..
node_cpu_seconds_total{cpu="1",mode="system"} 24.63
node_cpu_seconds_total{cpu="1",mode="user"} 5.71
node_cpu_seconds_total{cpu="2",mode="idle"} 42483.74
..
node_cpu_seconds_total{cpu="2",mode="system"} 14.84
node_cpu_seconds_total{cpu="2",mode="user"} 2.2
```
### По сетевой карте количество ошибок, полученных пакетов, байт, какие интерфейсы включены
```
node_network_receive_bytes_total{device="eth0"} 1.787987e+06
node_network_receive_bytes_total{device="lo"} 833643
node_network_receive_packets_total{device="eth0"} 11950
node_network_receive_packets_total{device="lo"} 485
node_network_receive_errs_total{device="eth0"} 0
node_network_receive_errs_total{device="lo"} 0
node_network_up{device="eth0"} 1
node_network_up{device="lo"} 0
```
### По файловой системе количество доступных байт (avail и free), размер разделов
```
node_filesystem_avail_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} 2.6942795776e+10
node_filesystem_avail_bytes{device="/dev/sda2",fstype="ext4",mountpoint="/boot"} 8.10192896e+08
..
node_filesystem_avail_bytes{device="vagrant",fstype="vboxsf",mountpoint="/vagrant"} 2.346934272e+10
node_filesystem_free_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} 2.8650713088e+10
node_filesystem_free_bytes{device="/dev/sda2",fstype="ext4",mountpoint="/boot"} 8.80656384e+08
..
node_filesystem_free_bytes{device="vagrant",fstype="vboxsf",mountpoint="/vagrant"} 2.3492182016e+10
node_filesystem_size_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} 3.315785728e+10
node_filesystem_size_bytes{device="/dev/sda2",fstype="ext4",mountpoint="/boot"} 1.02330368e+09
..
node_filesystem_size_bytes{device="vagrant",fstype="vboxsf",mountpoint="/vagrant"} 2.549308416e+11
```
### Количество доступной памяти (avail и free)
```
node_memory_MemAvailable_bytes 1.230065664e+09
node_memory_MemFree_bytes 9.26015488e+08
```
# 3
Поправил конфиг `netdata.conf`, конфиг `vagrantfile`. Проброс получился.
![image](https://user-images.githubusercontent.com/97126500/152237220-a92d8e66-3f9c-4c50-b966-e2a7d4b8ab7f.png)
# 4
Да, видно по строчкам
```
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.000000] Hypervisor detected: KVM
```
`DMI (Desktop Management Interface) — интерфейс (API), позволяющий программному обеспечению собирать данные о характеристиках компьютера.`  
`KVM (Kernel-based Virtual Machine) — программное решение, обеспечивающее виртуализацию в среде Linux на платформе x86, которая поддерживает аппаратную виртуализацию на базе Intel VT (Virtualization Technology) либо AMD SVM (Secure Virtual Machine).`
# 5
```
vagrant@vagrant:~$ sudo sysctl -a | grep fs.nr_open
fs.nr_open = 1048576
```
Указывает на максимальное количество открытых файловых дескрипторов (ограничение системы). `ulimit -n` показывает мягкие ограниения для текущей сессии `bash`
```
vagrant@vagrant:~$ ulimit -n
1024
```
# 6
Получилось!  
```
root@vagrant:/# lsns
        NS TYPE   NPROCS   PID USER            COMMAND
4026531835 cgroup    129     1 root            /sbin/init
4026531836 pid       128     1 root            /sbin/init
4026531837 user      129     1 root            /sbin/init
4026531838 uts       127     1 root            /sbin/init
4026531839 ipc       129     1 root            /sbin/init
4026531840 mnt       117     1 root            /sbin/init
4026531860 mnt         1    27 root            kdevtmpfs
4026531992 net       129     1 root            /sbin/init
4026532161 mnt         1   380 root            /lib/systemd/systemd-udevd
4026532162 uts         1   380 root            /lib/systemd/systemd-udevd
4026532178 mnt         1   614 systemd-network /lib/systemd/systemd-networkd
4026532188 mnt         1   616 systemd-resolve /lib/systemd/systemd-resolved
4026532190 mnt         2  2077 root            unshare -f --pid --mount-proc sleep 10m
4026532191 pid         1  2078 root            sleep 10m
4026532243 mnt         4   647 netdata         /usr/sbin/netdata -D
4026532244 mnt         1   645 root            /usr/sbin/irqbalance --foreground
4026532245 mnt         1   659 root            /lib/systemd/systemd-logind
4026532246 uts         1   659 root            /lib/systemd/systemd-logind
root@vagrant:~# nsenter --target 2078 --pid --mount
root@vagrant:/# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   5476   592 pts/1    S+   22:00   0:00 sleep 10m
root           2  0.1  0.2   7236  4268 pts/0    S    22:04   0:00 -bash
root          14  0.0  0.2   8892  3404 pts/0    R+   22:04   0:00 ps aux
root@vagrant:/#
```
# 7
Это fork-бомба, запускающих два своих экземпляра и тд
В `dmesg` находим
`[ 4195.927611] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-5.scope`
```
vagrant@vagrant:~$ cat /proc/sys/kernel/pid_max
4194304
vagrant@vagrant:~$ ulimit -u
5700
```
`cgroup - control group based traffic control filter`
```
vagrant@vagrant:~$ ulimit -u
5700
vagrant@vagrant:~$ ulimit -u 5699
vagrant@vagrant:~$ ulimit -u
5699
```
