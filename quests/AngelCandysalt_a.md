This objective is well guided. So I have only included the solutions:

<u>Task 1:</u> 

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 process_name="/usr/bin/git"
```

Click on interesting fields - CommandLine: git status

<u>Task 2:</u>

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 process_name="/usr/bin/git" CommandLine="*partnerapi*"
```

Click on interesting fields - CommandLine: git@github.com:elfnp3/partnerapi.git

<u>Task 3:</u> 

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 process_name="/usr/bin/docker" CurrentDirectory="*partnerapi*"
```

Click on interesting fields - CommandLine: docker compose up

<u>Task 4:</u>

```
index=main sourcetype=github_json
```

Click on interesting fields - repository.url: https://github.com/elfnp3/dvws-node 

Looking for the dvws-project inside GitHub results in the URL: https://github.com/snoopysecurity/dvws-node

<u>Task 5:</u>

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 process_name="/usr/bin/node"
```

Click on interesting fields - CommandLine: holiday-utils-js

<u>Task 6:</u>

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=3 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443)
```

Click on interesting fields - CommandLine: /usr/bin/nc.openbsd

<u>Task 7:</u>

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=3 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443) process_name="/usr/bin/nc.openbsd"

index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443) process_name="/usr/bin/nc.openbsd"

index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443) ParentProcessId=6788
```

Click on interesting fields - CommandLine: 6 (cat /home/eddie/.aws/credentials /home/eddie/.ssh/authorized_keys /home/eddie/.ssh/config /home/eddie/.ssh/eddie /home/eddie/.ssh/eddie.pub /home/eddie/.ssh/known_hosts)

<u>Task 8:</u>

```
index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443) ProcessId=6788

index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443) ProcessId=6784

index=main sourcetype=journald source=Journald:Microsoft-Windows-Sysmon/Operational EventCode=1 user=eddie NOT dest_ip IN (127.0.0.*) NOT dest_port IN (22,53,80,443) ProcessId=6783
```

Click on interesting fields - CommandLine: preinstall.sh
