# Services

## Create Service
1. Navigate to `/etc/systemd/system`
2. Create a new file `servicename.service`

In the new file enter something like this 
```
[Unit]
Description=Work Tracker API

[Service]
ExecStart=/usr/bin/dotnet API.dll
WorkingDirectory=/home/theodor349/WorkTracking/API
Restart=on-failure
SyslogIdentifier=worktrackerapi
PrivateTmp=true
User=theodor349

[Install]
WantedBy=multi-user.target
```
