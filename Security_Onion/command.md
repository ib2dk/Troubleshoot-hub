# Commands Used During Security Onion Troubleshooting (*Bash*)

## Check Security Onion Status
```bash
sudo so-status
```
# Check System Boot Status
```bash
systemctl is-system-running
```
# Check Salt Minion Status
```bash
sudo systemctl status salt-minion
```
# Check Internet Connectivity
```bash
ping -c 2 google.com
```
# Check Disk Usage
```bash
df -h
```
# Check Running Salt Jobs
```bash
sudo salt-call --local saltutil.running
```
# List Salt Processes
```bash
ps aux | grep salt
```
# Stop Salt Minion
```bash
sudo systemctl stop salt-minion
```
# Kill Stuck Salt Process
```bash
sudo kill -9 <PID>
```
# Clear Salt Job Cache
```bash
sudo rm -rf /var/cache/salt/minion/proc/*
sudo rm -rf /var/cache/salt/minion/files/*
```
# Restart Salt Minion
```bash
sudo systemctl start salt-minion
```
# Manually Run Highstate
```bash
sudo salt-call state.highstate
```
# Watch Highstate Output in Real Time
```bash
sudo salt-call state.highstate -l info
```
# Check Security Onion Services
```bash
sudo so-status
```
