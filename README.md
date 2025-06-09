# Ansible---Server-Hardening
Deep Linux Server Hardening with Ansible

This project contains an Ansible playbook for deeply hardening a RHEL-based Linux server. The playbook includes SSH configuration, SELinux, Fail2Ban firewall setup, kernel hardening, service restrictions, auditing, auto-updates, and reporting.

 ****Be sure to change file paths and parameters as needed to match your system.*****

 
 ****Console Access will be the only way to remote in, be sure to take snapshots of the server or be logged in on another terminal. Reset setting manually if needed by editing '/etc/yum.conf' /etc/selinux

Features:

SSH lockdown (no root, no password login)

Kernel-level sysctl security tuning

Disable legacy/risky services

Enforce secure umask and file permissions

Firewall configuration with firewalld

Enable auto-updates with dnf-automatic

Enable audit logging and Fail2Ban

Generate and email security hardening reports

Activate SELinux (runtime and config)

🔧 Playbook Breakdown

1. Create Report Directories & Files

- Create `/home/dariusp/patching_reports/secure_servers`
- Create three files: `.report`, `.report.header`, `.report.body`

Purpose: Prepare structure for logging and reporting hardening status.

2. SSH Hardening

- Restrict SSH access to allowed users
- Set SSH port to 22
- Disable root login
- Disable password-based SSH logins

Purpose: Minimize remote access vectors and enforce key-based auth.

3. Disable Unnecessary Services

- Loop through `disable_services` to stop and disable legacy daemons

Purpose: Remove insecure services like telnet, rlogin, rsh.

4. Apply Kernel sysctl Hardening

- Use `sysctl` module to apply hardened values (e.g., disable IP forwarding, spoofing)

Purpose: Protect against networking attacks and enforce kernel-level security policies.

5. Filesystem & Core Dump Protections

- Set umask to 027 (restrict default file perms)
- Disable core dumps for all users

Purpose: Prevent data leakage from dumped memory or insecure defaults.

6. Install and Enable firewalld

- Install `firewalld`
- Enable and start the service
- Allow only the `ssh` service
- Restart firewall to apply rules

Purpose: Enforce network-level security using firewall rules.

7. Configure Auto Security Updates

- Install `dnf-automatic`
- Enable the systemd timer `dnf-automatic.timer`

Purpose: Keep system patched with critical updates automatically.

8. Install and Enable Auditd

- Install and start `auditd`
- Run `aureport` to verify logs are working
- Output log summary with `debug`

Purpose: Enable audit logging for traceability and compliance.

9. Enforce Critical File Permissions

- Set `/etc/passwd` to 0644
- Set `/etc/shadow` to 0000

Purpose: Lock down sensitive identity-related files.

10. Install and Configure Fail2ban

- Install and start `fail2ban`

Purpose: Protect from SSH brute-force and failed login abuse.

11. Create Report Header and Body

- Use `shell` to echo system metadata (hostname, kernel, uptime)
- Generate structured `.report.header` and `.report.body`

Purpose: Provide a detailed snapshot of the hardened system.

12. Compile and Convert Final Report

- Concatenate header and body into `.report`
- Copy `.report` into `.txt` file for easier sharing

Purpose: Final report generated for visibility and auditing.

13. Email the Report

- Install `s-nail`
- Use `mailx` to email report to administrator

Purpose: Automatically alert or notify via email post-hardening.

14. Activate SELinux

- Set SELinux to `enabled` in `/etc/selinux/config`
- Use `setenforce 1` to apply enforcement in real-time

Purpose: Enforce mandatory access controls to limit damage from compromised processes.

⚙️ Requirements

Rocky Linux / RHEL / CentOS

Ansible 2.10+

Python installed on target machine

SSH access to target

Your vars.yml should define:

allowed_ssh_users:
  - dariusp

disable_services:
  - telnet
  - rsh
  - rlogin

sysctl_hardening:
  net.ipv4.ip_forward: 0
  net.ipv4.conf.all.accept_source_route: 0
  net.ipv4.conf.all.accept_redirects: 0
  net.ipv4.conf.all.send_redirects: 0
  net.ipv4.conf.all.log_martians: 1
  net.ipv4.icmp_echo_ignore_broadcasts: 1
  net.ipv4.tcp_syncookies: 1

🚀 Run the Playbook

ansible-playbook secure_lockdown.yml --step (I like to use '--step' just to have more control over the configurations. Also, it is good practice for error handling!)

***Some flags that may be used*****

--step  (prompt before each task is ran)

--start-at-task"" (start at a specific task)

--ask-become-pass ---- (prompt for become password)

--ask-vault-pass ----- (prompt for vault password)






Example Report file included!




---

🌍 Use Cases

- Internal DevSecOps environments
- Chaos-ready automation (resilient provisioning)
- Infrastructure compliance bootstrapping

---

🧠 About

Created and maintained by Darius Powell
