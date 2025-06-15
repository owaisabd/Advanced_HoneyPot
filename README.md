# HONEYPY

![HONEPY Logo](/assets/images/honeypy-logo-black-text.png)

HONEYPY is a modular honeypot built with Python that simulates SSH and HTTP services to collect data from unauthorized access attempts. It captures attacker IPs, usernames, passwords, and commands, and presents them through a web-based dashboard for easy analysis. The project is designed for learning, research, and security testing purposes.

---

## Features

- SSH and HTTP honeypot modules
- Full logging of credentials and command inputs
- Visual dashboard using Dash (top IPs, usernames, passwords, commands)
- Optional country code lookup for attacker IPs
- Support for `--tarpit` mode to slow brute-force attacks
- systemd service support for background operation
- Custom credentials and port binding
- Easy deployment and environment-based configuration

---

## Installation

1. **Clone the repository**

```bash
git clone https://github.com/your-username/ssh_honeypy.git
cd ssh_honeypy
```

2. **Set permissions**

```bash
chmod 755 honeypy.py
```

3. **Generate SSH host key**

```bash
mkdir static
cd static
ssh-keygen -t rsa -b 2048 -f server.key
```

Make sure the file is named `server.key` and resides in the `static` folder.

---

## Usage

Run the honeypot using:

```bash
python3 honeypy.py -a 0.0.0.0 -p 22 --ssh
```

**Arguments:**
- `-a`, `--address`: Bind address (e.g., `0.0.0.0`)
- `-p`, `--port`: Port to listen on
- `--ssh` or `--http`: Honeypot type
- `-u`, `--username`: (Optional) Enforce specific username
- `-w`, `--password`: (Optional) Enforce specific password
- `-t`, `--tarpit`: (SSH only) Send an endless banner to stall brute-force attempts

**Note:** Ports like 22 require `sudo`. Make sure root has access to required Python packages:

```bash
sudo pip install -r requirements.txt
```

---

## Logging

Logs are saved in the `log_files/` directory:

- `cmd_audits.log`: IP, username, password, and all entered commands (SSH)
- `creds_audits.log`: IP, username, and password (SSH login attempts)
- `http_audit.log`: IP, username, and password (HTTP login attempts)

---

## Honeypot Types

### SSH Honeypot

Simulates an interactive shell. Use the `--tarpit` flag to send a long SSH banner and keep attackers stalled.

### HTTP Honeypot

Imitates a WordPress `wp-admin` login page using Flask. Captures submitted credentials. The default pair is `admin / deeboodah`, which leads to a Rick Roll. You can override the credentials using `-u` and `-w`.

Port defaults to `5000`, but you can change it using `-p`.

---

## Dashboard

To view visual statistics from the log files:

```bash
python3 web_app.py
```

Then go to: [http://127.0.0.1:8050](http://127.0.0.1:8050)

Displays:
- Top 10 IP addresses
- Top usernames and passwords
- Most executed commands
- Tabular view of all entries

**Country Code Lookup:**
- Set `COUNTRY=True` in `public.env` to enable country code lookup using [CleanTalk API](https://cleantalk.org/help/api-ip-info-country-code)
- Subject to rate limits (1,000 lookups per minute)
- Set to `False` if slow or if rate-limited

---

## Running on a VPS

To host HONEYPY on a VPS:

- Open necessary ports in your firewall:
  - `22` (SSH honeypot)
  - `5000` (HTTP honeypot)
  - `8050` (dashboard)
  - `80` if redirecting traffic
- Use `ufw` or your provider’s firewall:

```bash
sudo ufw enable
sudo ufw allow 22
sudo ufw allow 5000
sudo ufw allow 8050
```

---

## Running in Background with systemd

There’s a sample systemd service file in the repo.

1. Edit `honeypy.service`:

```
ExecStart=/usr/bin/python3 /path/to/honeypy.py -a 0.0.0.0 -p 22 --ssh
```

2. Move the file:

```bash
sudo cp honeypy.service /etc/systemd/system/
```

3. Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable honeypy.service
sudo systemctl start honeypy.service
```

---

## Inspiration

This project was inspired by [Grant Collins' SSH honeypot video](https://youtu.be/tyKyLhcKgNo), which provided a helpful foundation. From there, the project was extended with additional features including:

- Multi-protocol support (SSH and HTTP)
- Logging for credentials and commands
- A visual dashboard for log analysis
- Tarpit functionality
- systemd integration
- Environment variable support

While the initial idea came from the video, most of the code and design were extended independently for a more complete and customizable honeypot system.

---

## Future Work

- [x] SSH support
- [x] HTTP honeypot with spoofed login
- [x] Visual dashboard
- [x] systemd integration
- [x] Tarpit support
- [ ] Dynamic dashboard updates
- [ ] Docker deployment
- [ ] Dashboard as a separate service
- [ ] More protocols (Telnet, HTTPS, SMTP, RDP, DNS)

---

## Helpful Resources

- https://securehoney.net/blog/how-to-build-an-ssh-honeypot-in-python-and-docker-part-1.html
- https://medium.com/@abdulsamie488/deceptive-defense-building-a-python-honeypot-to-thwart-cyber-attackers-2a9d2ced2760
- https://gist.github.com/cschwede/3e2c025408ab4af531651098331cce45
- https://www.hostinger.com/tutorials/how-to-change-ssh-port-vps
