# Linux SOC Lab

This repository contains my hands-on Linux practice for a Junior SOC Analyst / SOC L1 role.

I use this lab to practice Linux commands, processes, network activity, logs and basic incident investigation.

## Environment

- Ubuntu 24.04
- VirtualBox
- Linux CLI
- Local lab environment

## Linux basics

I practiced:

- Linux filesystem
- users and groups
- UID and GID
- file permissions
- `chmod`
- `sudo`
- processes
- PID and PPID
- process tree
- network interfaces
- routing

Commands used:

```bash
whoami
id
pwd
ls
cd
chmod
ps
ps -ef
ps aux
ip addr
ip route
```

## Process analysis

I created test processes and analyzed them with Linux commands.

Example:

```text
bash
├── python3
└── sleep
```

I used:

```bash
ps -eo user,pid,ppid,cmd
ps -fp
```

I practiced:

- finding PID
- finding PPID
- understanding parent and child processes
- identifying user context
- terminating processes with `kill`

Important lesson:

```text
Process name alone does not prove that the process is safe.
```

## Network analysis

I started a local Python HTTP server and checked the listening port.

Example:

```bash
python3 -m http.server 9090 --bind 127.0.0.1
```

I used:

```bash
ss -ltnp
sudo ss -ltnp
```

Example result:

```text
127.0.0.1:9090 LISTEN
```

I learned the difference between:

```text
LISTEN
ESTABLISHED
```

Important lessons:

```text
LISTEN does not mean active connection.
External connection does not automatically mean C2.
Unknown IP does not automatically mean IOC.
```

## HTTP log analysis

I used `curl` to send requests to a local HTTP server.

Example:

```bash
curl http://127.0.0.1:9090/test.txt
```

Example log:

```text
GET /test.txt HTTP/1.1 200
```

Important lesson:

```text
HTTP 200 means the request was successful.
HTTP 200 does not mean the activity is safe.
```

## Sudo and authentication logs

I practiced working with sudo and Linux logs.

Commands used:

```bash
sudo whoami
journalctl
grep
```

I analyzed information such as:

```text
USER
PWD
COMMAND
authentication failure
session opened
session closed
```

Example:

```text
User: daniel
sudo
USER=root
COMMAND=/usr/bin/whoami
```

Important lessons:

```text
sudo activity does not automatically mean an attack.
root process does not automatically mean privilege escalation.
```

## Application logs

I redirected Python HTTP server output to a log file.

Example:

```bash
python3 -m http.server 8080 > server.log 2>&1 &
```

Then I analyzed the log:

```bash
cat server.log
```

This helped me understand the difference between:

```text
system logs
application logs
```

## Investigation example

In one lab I analyzed this process structure:

```text
sudo
↓
bash script
├── python3 HTTP server
└── sleep
```

The Python process was listening on:

```text
127.0.0.1:9090
```

I used:

```bash
ps -eo user,pid,ppid,cmd
sudo ss -ltnp
journalctl
cat
grep
```

to build the process tree and activity timeline.

## Investigation method

During analysis I separate information into three parts.

### FACT

Information directly confirmed by logs or command output.

Example:

```text
Process: python3
User: root
Address: 127.0.0.1
Port: 9090
State: LISTEN
```

### HYPOTHESIS

A possible explanation that needs more evidence.

Example:

```text
The process may be part of a test or administrative activity.
```

### ASSESSMENT

My current evaluation of the activity.

Examples:

```text
Benign
Suspicious
Confirmed Malicious
```

## Key lessons

- File created does not mean file executed.
- Process created confirms process execution.
- Process name does not prove legitimacy.
- Root activity is not automatically malicious.
- External connection is not automatically C2.
- Unknown IP is not automatically an IOC.
- HTTP 200 does not mean benign activity.
- Process termination does not mean evidence was deleted.
- Timeline and context are important during investigation.

## Tools and commands

- `ps`
- `ss`
- `ip`
- `journalctl`
- `grep`
- `sudo`
- `curl`
- `kill`
- `chmod`
- `cat`
- Python HTTP server

## Goal

The goal of this lab is to improve my Linux skills and learn how Linux processes, logs and network activity can be analyzed in a SOC environment.
