# Security Onion Troubleshooting: Highstate Stuck After Reboot

This write-up documents how I troubleshooted and fixed a **Security Onion** instance that failed to complete startup after being powered on in my home lab.

After a few days away, I returned to my lab and noticed that Security Onion was running but the **web interface was not accessible**. Instead of reinstalling immediately, I decided to investigate and understand what went wrong.

## The Problem

On login, Security Onion displayed the usual message showing the URL for the web interface, but attempting to access it from my Ubuntu machine failed.

Running the status check showed that the system was still in a *starting* state. A setup process called **highstate** had never completed, which explained why services depending on it were not fully available.

At this point, it was clear that something in the initialization process was blocked.

## Initial Checks

Before touching anything critical, I verified the basics:

- The system had internet access
- Disk usage was normal
- Core services were running

Since Security Onion relies heavily on **SaltStack (Salt)** for configuration and orchestration, I focused my attention there.

## Root Cause

While reviewing logs and Salt behavior, I discovered that **Salt was stuck holding a lock** from a previous run. Because of this, `state.highstate` could not complete, and Security Onion remained in a partially initialized state.

This caused:
- Highstate to never finish
- The web UI to remain inaccessible
- `so-status` to keep reporting the system as “starting”

## The Fix

To resolve the issue, I:

- Stopped the Salt minion service  
- Identified and killed the stuck Salt process  
- Cleared the Salt job cache  
- Restarted Salt  
- Manually reran `state.highstate`

After rerunning highstate, it completed successfully with no errors.

## Verification

Once highstate completed, I checked the system status again.
This time, all services were running normally, and the system no longer reported an incomplete setup.
I refreshed the browser and was able to access the **Security Onion web interface**, confirming that the issue was fully resolved.

## Outcome

Security Onion is now fully operational and actively monitoring network traffic in my home lab.

This issue turned out to be a good reminder that:
- Highstate problems often point back to Salt
- Reinstalling should be a last resort
- Logs and patience save a lot of time

## Environment

- Security Onion (Standalone)
- Oracle Linux Server 9
- Home Lab deployment

## Notes

All commands used during troubleshooting are documented in `command.md`.
