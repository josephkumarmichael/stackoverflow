The below code snippet will create Service in CentOS 7 using Ansible

## Code

/tasks/main.yml
```
- name: TeamCity | Create environment file
  template: src=teamcity.env.j2 dest=/etc/sysconfig/teamcity
- name: TeamCity | Create unit file
  template: src=teamcity.service.j2 dest=/lib/systemd/system/teamcity.service mode=644
  notify:
    - reload systemctl
- name: TeamCity | Start TeamCity
  service: name=teamcity.service state=started enabled=yes
```


/templates/teamcity.service.j2
```
[Unit]
Description=JetBrains TeamCity
Requires=network.target
After=syslog.target network.target
[Service]
Type=forking
EnvironmentFile=/etc/sysconfig/teamcity
ExecStart={{teamcity.installation_path}}/bin/teamcity-server.sh start
ExecStop={{teamcity.installation_path}}/bin/teamcity-server.sh stop
User=teamcity
PIDFile={{teamcity.installation_path}}/teamcity.pid
Environment="TEAMCITY_PID_FILE_PATH={{teamcity.installation_path}}/teamcity.pid"
[Install]
WantedBy=multi-user.target
```

\templates\teamcity.env.j2
```
TEAMCITY_DATA_PATH="{{ teamcity.data_path }}"
```

\handlers\main.yml
```
- name: reload systemctl
  command: systemctl daemon-reload
```

## Reference :

 - Ansible playbook structure: http://docs.ansible.com/ansible/playbooks_intro.html
 - SystemCtl: https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units
