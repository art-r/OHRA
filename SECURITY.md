# Security Policy

## Overview

This repository contains **source code only** for running a honeypot inside a Docker container.  
It does **not** include any hosted service, infrastructure, or managed environment.

All deployment, configuration, and operation of the honeypot are the sole responsibility of the user running the code.

This is a **hobby / research project**, provided as-is.

## Scope

The following are considered **in scope** for security reports:

- Vulnerabilities in this repository that allow **escape from the Docker container**
- Bugs that enable **arbitrary code execution on the host** when using the project as documented
- Unintended **data leakage from the container to the host**
- Supply-chain issues introduced directly by this repository (e.g., malicious scripts, unsafe defaults)

The following are **out of scope**:

- Attacks against a deployed honeypot instance
- Compromise of systems running the honeypot
- Issues caused by insecure Docker configuration, exposed ports, or host-level permissions
- Observed malicious behavior targeting the honeypot
- Vulnerabilities in third-party dependencies unless introduced or modified by this project
- Denial-of-service conditions affecting the honeypot service itself

## Reporting a Vulnerability

If you believe you have found a **security issue in the source code** that could impact users, please report it responsibly.

**Preferred methods:**
- Open a private security advisory as described here: https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability

Please include:
- A clear description of the issue
- Steps to reproduce using this repository
- Expected vs. actual behavior
- Potential impact on a typical Docker-based deployment

## Disclosure Policy

- Please **do not publicly disclose** security issues without prior coordination.
- This project does **not** offer a bug bounty.
- Issues are reviewed and addressed on a **best-effort basis**, as time permits.

## Deployment Responsibility

Users of this project are responsible for:

- Securing their Docker environment and host system
- Complying with applicable laws and regulations
- Understanding that honeypots intentionally attract malicious activity
- Reviewing logs and captured data for sensitive information

The project author does **not** operate, monitor, or collect data from any deployed instances.

## Data & Privacy

Any data collected by a running honeypot instance (such as IP addresses, payloads, or user input) is collected **entirely by the deployer’s environment**.

The author of this repository does not receive, store, or process any such data.

## Legal Disclaimer

This software is provided **"as is"**, without warranty of any kind.

The author assumes **no liability** for:
- Misuse of the code
- Damage to systems or networks
- Legal consequences arising from deployment or operation

Use at your own risk.

## Supported Versions

Only the latest commit on the default branch is considered supported.

## Contact

Use the issue tracker.

For general bugs or feature requests, please use the public issue tracker.
