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

## Commands used

```bash
ps -eo user,pid,ppid,cmd
sudo ss -ltnp
journalctl
cat /tmp/soc-check.log
cat /tmp/soc-http.log
```

## Network activity

The Python HTTP server was listening on:

```text
127.0.0.1:9090
```

State:

```text
LISTEN
```

This means that the process was waiting for local TCP connections.

Because the server was bound to `127.0.0.1`, it was only reachable from the local host.

## HTTP activity

The application log contained a request similar to:

```text
GET /test.txt HTTP/1.1 200
```

The status code `200` shows that the request was processed successfully.

It does not prove that the activity was safe.

## Sudo activity

The system logs showed that the script was started through sudo.

Observed context:

```text
User: daniel
Target user: root
Command: /tmp/.soc-check.sh
```

## FACTS

- The script was started through sudo.
- The Bash process executed the script.
- A Python HTTP server was started.
- The Python process was running as root.
- The server was listening on `127.0.0.1:9090`.
- The HTTP server received a GET request.
- The request returned HTTP status `200`.

## HYPOTHESES

Possible explanations:

- The activity may be part of an administrative or test task.
- The HTTP server may have been started intentionally for local testing.

There was no evidence of an external network connection in this scenario.

## ASSESSMENT

```text
Benign / Lab Activity
```

The observed activity matched the expected behavior of the test script.

There was no evidence of C2 communication or external access.

## Lessons learned

- Parent and child processes should be reconstructed using PID and PPID.
- Processes with the same PPID are siblings, not a serial chain.
- Root context does not automatically mean malicious activity.
- `127.0.0.1` means localhost.
- A listening port is not the same as an active external connection.
- HTTP 200 means successful processing, not safe activity.
- Network conclusions should be based on actual network evidence.
