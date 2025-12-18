# OHRA
> One Honeypot to Rule them All  [![security: bandit](https://img.shields.io/badge/security-bandit-yellow.svg)](https://github.com/PyCQA/bandit)

## Description

An extensible, modular, multi-protocol honeypot framework using Large Language Models (LLMs) for dynamic deception. Supports protocols such as SSH, Telnet, HTTP, FTP, SMTP, SNMP, and IPP.

## ✨ Features

- **Multi-Protocol Support**: Deploy honeypots for various services and protocols due to modular architecture
- **Efficient Session Management**: Logs and LLM response contexts are session aware - sessions are identified by the source IP address
- **Optional Script/Malware Preservation**: Detection and Preservation of the content behind IP addresses and URLs in input commands
- **Easy LLM-Provider Switching**: Using the Langchain library it is easy to switch to other LLM model providers (incl. local-hosted)
- **Centralized Logging**: Unified log management and processing (JSON logs)
- **Docker-Based Deployment**: Easy setup and scalability using Docker Compose
- **Configurable Ports**: Flexible port configuration for each honeypot component

## Prerequisites

- Docker Engine 20.10+
- Docker Compose 2.0+
- LLM API Key (e.g., OpenAI for LLM functionality)
- Minimum 2GB RAM
- sufficient available disk space (depending on how long the honeypot will run, the log sizes will increase)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/art-r/OHRA
cd OHRA
```

### 2. Configure Environment

Create a `.env` file in the `src` directory:

```bash
cp .env.example .env
```

- **Important:** If you want to run the system locally — i.e., without exposing the honeypots to your device's public IP address — you should use `docker-compose_local.yml`, which does **not** run the containers in host networking mode.  
  To do this, rename the default `docker-compose.yml` to `docker-compose.yml.INACTIVE`, and rename `docker-compose_local.yml` to `docker-compose.yml`.\
  Host networking is used in production to allow honeypots to correctly capture the source IP addresses of incoming connections.

- Modify the port mappings in `src/docker-compose.yml` to adjust listening ports for each honeypot.
Adjust the parameter `LISTENING_PORT` for each honeypot to modify on which port the respective honeypot will listen.

- Edit the `.env` file and add your API key:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

If you want to edit the LLM model in the background, edit the respective config lines in `src/LLMHandler/llm_wrapper.py`:

```python
# UPDATE THIS FOR DIFFERENT MODELS
# MODEL = "gpt-4.1-nano"
MODEL = "gpt-4o-mini"
PROVIDER = "openai"
```

### 3. Deploy with Docker Compose

Navigate to the source directory and start the services:

```bash
cd src/
docker compose up -d
```

### 4. Verify Deployment

Check that all services are running:

```bash
docker compose ps
```


## Usage

### Monitoring Logs

View real-time logs from all components:

```bash
docker compose logs -f
```

View logs from a specific service:

```bash
docker compose logs -f ssh-pot
```

### Accessing Log Files

Honeypot Logs are stored in the `logs` docker volume and can be accessed there:
- The system logs are stored in one `<current-date>.log` file which is continuously updated.
- The session logs are stored per session (identified based upon the source ip address) under a respective `<protocol>-<unique_session_id>.json` file

If the honeypot is configured to preserve url content they are downloaded and stored in the `data` docker volume, using the SHA-256 sum of the respective downloaded file content as `<SHA-256-sum>.DATA`


### ⚙️ Default Honeypot Config Overview

| Protocol | Default Port | Container Name | Notes                     |
| -------- | ------------ | -------------- | ------------------------- |
| SSH      | 22           | ssh-pot        | -                         |
| Telnet   | 23           | telnet-pot     | -                         |
| HTTP     | 80           | http-pot       | -                         |
| FTP      | 21           | ftp-pot        |  No support for SSL       |
| SMTP     | 25           | smtp-pot       |  No support for SSL       |
| SNMP     | 161          | snmp-pot       | UDP-based; No output generation due unknown bug! |
| IPP      | 631          | ipp-pot        | Experimental; LLMs cannot process binary data    |
| Log API  | 8000         | log-handler    | Localhost only             |
| LLM API  | 8001         | llm-handler     | Localhost only            |


## 🧪 What to Expect After Startup

Once deployed, the honeypot containers:
- Start listening on the mapped ports (see above)
- Automatically log all interactions to the `logs/` volume (inside `session_logs/` and `error_logs/`)
- Use LLMs to respond to attacker input in real time
- Create per-session state memory to simulate persistent system behavior
    

You can test the honeypots locally using tools like:

```bash
# SSH test
ssh testuser@127.0.0.1 -p 22

# Telnet test
telnet 127.0.0.1 23

# HTTP test
curl http://127.0.0.1:80

# FTP
ftp 127.0.0.1 21

# SMTP
telnet 127.0.0.1 2525
HELO example.com

# IPP
curl -v http://127.0.0.1:631
## optionally use cups for extended testing

# SNMP
## SNMP is more complex but potentially can use tools like snmpwalk
## Further SNMP currently does not respond at all due to unknown reasons
```

## 🔍 Sample Logs
### Session Log
```json
{ 
    "time": "2025-06-13 01:37:14", 
    "type": "NEW CONNECTION", 
    "ip": "<ip-redacted>", 
    "protocol": "ssh", 
    "content": "<New Connection>"
}, 
{   "time": "2025-06-13 01:37:21", 
    "type": "LOGIN", 
    "ip": "<ip-redacted>", 
    "protocol": "ssh", 
    "content": "root - root"
},
```

### Application Log
```log
2025-05-15 12:33:02 INFO: IPPPot class successfully initialized
2025-05-15 12:33:02 INFO: SSHPot class successfully initialized
2025-05-15 12:33:02 INFO: SMTPPot class successfully initialized
2025-05-15 12:33:02 INFO: FTPPot class successfully initialized
2025-05-15 12:33:02 INFO: HTTPPot class successfully initialized
2025-05-15 12:33:02 INFO: TelnetPot class successfully initialized
2025-05-15 12:33:03 INFO: SNMPPot class successfully initialized
2025-05-15 12:33:04 INFO: HTTPPot is running and listening on 0.0.0.0:80
[...]
```

## Repository Overview

```
.
|____LICENSE
|____pyproject.toml # Dependency file
|____poetry.lock # Dependency file with exact versions
|____.env # Environment file for API keys
|____src # The source directory of the Honeypot Prototype
| |____docker-compose.yml # docker compose file for the honeypot
| |____LogHandler # The Log Handler component
| |____LLMHandler # The LLM Handler component
| | |____prompts # system prompts for all Honeypot types
| |____Honeypots # The individual Honeypot implementations
| | |____DockerfileTelnet
| | |____ipp_honeypot.ini
| | |____DockerfileSNMP
| | |____DockerfileSSH
| | |____ssh_honeypot.py
| | |____telnet_honeypot.py
| | |____DockerfileIPP
| | |____smtp_honeypot.py
| | |____snmp_honeypot.py
| | |____http_honeypot.ini
| | |____ftp_honeypot.py
| | |____DockerfileSMTP
| | |____DockerfileHTTP
| | |____DockerfileFTP
| | |____http_honeypot.py
| | |____base_honeypot.py
| | |____ipp_honeypot.py
- -
```

## Honeypot Components

### Currently Supported Services

- **SSH Honeypot**: Simulates SSH service with credential logging
- **HTTP Honeypot**: Web server honeypot with request analysis
- **FTP Honeypot**: FTP service simulation
- **Telnet Honeypot**: Legacy telnet service emulation
- **SMTP Honeypot**: Email service honeypot
- **IPP Honeypot**: IPP service honeypot
- ~~**SNMP Honeypot**: SNMP service honeypot~~ (currently not working correctly; can log input but unable to respond; root issue unknown)

### Adding New Honeypots

1. Create a new python file in `src/Honeypots/` named after the honeypot
2. Implement the honeypot service in Python, by extending the base_honeypot class (implements base functionalities for logging and the LLM connection)
3. Add Dockerfile for the new honeypot
4. Update `docker-compose.yml` to include the new honeypot
5. Add the corresponding protocol system prompt as a txt file in `src/LLMHandler/prompts/` and include the prompt path in the `src/LLMHandler/llm_wrapper.py` file
6. Update documentation (if needed) & test the new honeypot

## Security Considerations

- **Network Isolation**: Run honeypots in isolated network segments
- **Resource Limits**: Configure appropriate CPU and memory limits
- **Log Rotation & Backup**: Implement log rotation to prevent disk space issues and regularly backup honeypot logs
- **Regular Updates**: Keep Docker images and dependencies updated

## Troubleshooting

### Common Issues

**Services won't start:**
```bash
docker compose logs [service-name]
docker system prune  # Clean up Docker resources
```

**Port conflicts:**
- Check for existing services using the same ports
- Modify port mappings in docker-compose.yml

**OpenAI API errors:**
- Verify API key is correct and has sufficient credits
- Check network connectivity to respective API services

**High resource usage:**
- Adjust resource limits in docker-compose.yml
- Monitor with `docker stats`

### Debug Mode

Enable debug logging:

```bash
export LOG_LEVEL=DEBUG
docker compose up
```

## Development

### Setting Up Development Environment

[Poetry](https://python-poetry.org/) is used for dependency management

```bash
# Install development dependencies
poetry install
```

## License

This project is licensed under the BSD 3-Clause License - see the [LICENSE](LICENSE) file for details.
