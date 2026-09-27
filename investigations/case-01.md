# Case 01 — Process and Network Investigation

## Scenario

A test script was started with sudo in a Linux lab.

The script started:

- a Bash process
- a Python HTTP server
- a sleep process

The goal was to analyze the process tree, user context, network listener and related logs.

## Observed process tree

```text
sudo
↓
bash /tmp/.soc-check.sh
├── python3 -m http.server 9090 --bind 127.0.0.1
└── sleep 600
```

The Python process was running as root.
Commands used
ps -eo user,pid,ppid,cmd
sudo ss -ltnp
journalctl
cat /tmp/soc-check.log
cat /tmp/soc-http.log

Network activity
The Python HTTP server was listening on:
127.0.0.1:9090

State:
LISTEN

This means that the process was waiting for local TCP connections.
Because the server was bound to 127.0.0.1, it was only reachable from the local host.
HTTP activity
The application log contained a request similar to:
GET /test.txt HTTP/1.1 200

The status code 200 shows that the request was processed successfully.
It does not prove that the activity was safe.
Sudo activity
The system logs showed that the script was started through sudo.
Observed context:
User: daniel
Target user: root
Command: /tmp/.soc-check.sh

FACTS
- the script was started through sudo
- the Bash process executed the script
- Python HTTP server was started
- the Python process was running as root
- the server was listening on 127.0.0.1:9090
- the HTTP server received a GET request
- the request returned HTTP status 200
HYPOTHESES
Possible explanations:
- the activity may be part of an administrative or test task
- the HTTP server may have been started intentionally for local testing
There was no evidence of an external network connection in this scenario.
ASSESSMENT
Benign / Lab Activity

The observed activity matched the expected behavior of the test script.
There was no evidence of C2 communication or external access.
Lessons learned
- Parent and child processes should be reconstructed using PID and PPID.
- Processes with the same PPID are siblings, not a serial chain.
- Root context does not automatically mean malicious activity.
- 127.0.0.1 means localhost.
- A listening port is not the same as an active external connection.
- HTTP 200 means successful processing, not safe activity.
- Network conclusions should be based on actual network evidence.
