# Central Logging

## Server

Pick a server.
I used pi-12-pp which is the first control node also.

### On Logging Server

Create a place to hold all the logs.

``` bash
sudo mkdir /var/log/central
sudo chown syslog:adm /var/log/central
```

Rsyslog will use TCP/UDP port 514, but you need to enable it.
Edit /etc/rsyslog.conf, and make sure these lines look like this (by uncommenting them):

``` bash
sudo nano /etc/rsyslog.conf

# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")

```

Next create config to tell rsyslog to put all logs in previously created folder, create /etc/rsyslog.d/45-central.conf

``` bash
sudo nano /etc/rsyslog.d/45-central.conf

$template RemoteLogs,"/var/log/central/%HOSTNAME%.log"
*.*  ?RemoteLogs
```

This will put all logs under /var/log/central/<hostname>.log

Last thing, and this is kind of optional, we need to tell logrotate about this, and have it rotate the logs, so you don't end up with 100+MB text files.

Create file /etc/logrotate.d/central

``` x
/var/log/central/*.log
{
        rotate 4
        weekly
        missingok
        notifempty
        compress
        delaycompress
        sharedscripts
        postrotate
                invoke-rc.d rsyslog rotate >/dev/null 2>&1 || true
        endscript
}
```

    rotate - How many rotated copies to keep before removing the oldest one.
    weekly - Rotate log every 7 days.
    missingok - If the log file is missing, go on to the next one without issuing an error message.
    notifempty - Do not rotate the log if it is empty.
    compress - Gzip the logs.
    delaycompress - Postpone compression of the previous log file to the next rotation cycle.
    sharedscripts - Because we are going to use wildcard, we need this argument, telling logrotate this setting is for multiple logs.
    postrotate - What to do after rotation is finished, in this case invoke rsyslog rotate.

Some more info about options: https://linux.die.net/man/8/logrotate

Restart rsyslog

``` bash
sudo systemctl restart rsyslog
```

That’s it for a server, no need to restart logrotate; that will be run via cron.

## lnav

Just a nifty little program to watch your logs in real time, with filters and so on.

``` bash
sudo apt install lnav
lnav /var/log/central/*.log
```
