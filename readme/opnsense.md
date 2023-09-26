### How-to install to opnsense

1. Create file `/usr/local/etc/rc.d/openvpn_exporter`

```bash
#!/bin/sh

. /etc/rc.subr

name="openvpn_exporter" # How the service will be invoked from service          
rcvar="${name}_enable" # The variable in rc.conf that will allow this service to run
pidfile=/var/run/${name}.pid
openvpn_exporter_user=root

command="/usr/local/bin/openvpn_exporter --status-file /var/log/openvpn/status.log";
#command_args="&";

start_cmd="openvpn_exporter_start"
stop_cmd="openvpn_exporter_stop"
status_cmd="openvpn_exporter_status"

openvpn_exporter_start() {
  /usr/sbin/daemon -P ${pidfile} -r -f -u $openvpn_exporter_user $command
}

openvpn_exporter_stop() {
  if [ -e "${pidfile}" ]; then
    kill -s TERM `cat ${pidfile}`
  else
    echo "${name} is not running"
  fi
}

openvpn_exporter_status() {
  if [ -e "${pidfile}" ]; then
    echo "${name} is running as pid `cat ${pidfile}`"
  else
    echo "${name} is not running"
  fi
}

load_rc_config ${name}
run_rc_command "$1"

```

2. Create service file `/etc/rc.conf.d/openvpn_exporter`

```ini
openvpn_exporter_enable="YES"
```