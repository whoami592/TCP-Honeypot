# 🍯 TCP Honeypot

A lightweight, dependency-free **TCP honeypot** written in Python. It listens on a TCP port, presents a fake **SSH banner** to anyone who connects, records the connection and the first data they send, and then closes the session. It is designed to help defenders detect and log unauthorized connection attempts and scanning activity.

> **Coded by Mr Sabaz Ali Khan** — Cyber Security Engineer

---

## ⚠️ Disclaimer

This project is for **educational and ethical/defensive use only**. Deploy it only on systems and networks you own or have explicit written permission to monitor. The author is not responsible for any misuse or damage caused by this tool. Check your local laws and regulations regarding logging network traffic.

---

## ✨ Features

- Listens on any TCP port (default: `2222`)
- Sends a fake `SSH-2.0-OpenSSH_7.4p1` banner to lure scanners and bots
- Logs the source IP and port of every connection
- Captures the first chunk of data (up to 1024 bytes) sent by the client
- Multi-threaded: each connection is handled in its own thread
- Writes timestamped entries to `honeypot.log`
- Pure Python standard library — **no external dependencies**

---

## 📋 Requirements

- Python 3.6 or newer
- Works on Linux, macOS and Windows

---

## 🚀 Installation

```bash
git clone https://github.com/whoami592/TCP-Honeypot.git
cd <your-repo>
```

No `pip install` is needed.

---

## ▶️ Usage

```bash
python3 tcp_honeypot.py
```

Expected output:

```
Starting TCP Honeypot...
[*] Honeypot listening on 0.0.0.0:2222
[*] Connection from 192.168.1.50:51734
[*] Data from 192.168.1.50:51734: SSH-2.0-libssh_0.9.6
```

Press `Ctrl + C` to stop.

### Testing it locally

From another terminal:

```bash
nc 127.0.0.1 2222
```

or

```bash
telnet 127.0.0.1 2222
```

You should see the fake SSH banner, and a new entry will appear in `honeypot.log`.

---

## ⚙️ Configuration

Host and port are set in the `start_honeypot()` function:

```python
def start_honeypot(host='0.0.0.0', port=2222):
```

| Setting | Default   | Description                                          |
|---------|-----------|------------------------------------------------------|
| `host`  | `0.0.0.0` | Interface to bind to (`127.0.0.1` for local only)    |
| `port`  | `2222`    | TCP port to listen on                                |

> **Tip:** Ports below 1024 (such as 22) require root/administrator privileges. If you run the honeypot on port 22, move your real SSH service to another port first.

The fake banner can be changed in `handle_client()`:

```python
client_socket.send(b"SSH-2.0-OpenSSH_7.4p1 Debian-10+deb9u7\r\n")
```

---

## 📝 Log Format

Logs are saved in `honeypot.log` in the working directory:

```
2026-11-08 14:02:11,431 - Honeypot started on 0.0.0.0:2222
2026-11-08 14:02:35,882 - Connection from 192.168.1.50:51734
2026-11-08 14:02:35,990 - Data from 192.168.1.50:51734: SSH-2.0-libssh_0.9.6
```

---

## 🔍 How It Works

1. Creates a TCP socket and binds it to the configured host and port.
2. Accepts incoming connections in a loop.
3. For each connection, a new thread:
   - logs the client's IP address and port,
   - sends the fake SSH banner,
   - reads up to 1024 bytes from the client and logs them,
   - sends a short warning message and closes the connection.

> **Note:** This is a *low-interaction* honeypot. It does not implement the real SSH protocol — it only imitates the banner and records what the client sends first.

---

## 🛡️ Safety Recommendations

- Run it as a **non-root user** on a high port, and forward traffic to it if needed.
- Run it in an isolated environment (VM, container or separate VLAN).
- Never run it on a machine that stores sensitive data.
- Monitor and rotate `honeypot.log` so it cannot fill the disk.

---

## 🗺️ Possible Improvements

- Log rotation and JSON log output
- Connection timeouts and a maximum-connections limit
- Support for multiple ports and fake services (FTP, Telnet, HTTP)
- Alerts via email or Telegram
- GeoIP lookup for source addresses

---

## 🤝 Contributing

Pull requests and suggestions are welcome. Please open an issue first to discuss any major change.

---

## 👤 Author

**Mr Sabaz Ali Khan** — Cyber Security Engineer, Pakistan

*Coded by Mr Sabaz Ali Khan*

---

## 📄 License

Released under the [MIT License](LICENSE). Add a `LICENSE` file to your repository to use this license.
