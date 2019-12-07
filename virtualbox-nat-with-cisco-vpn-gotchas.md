#### Problem
When using oracle virtualbox, it is desirable to deploy linux guest on a windows host with NAT enabled (instead of bridge or other network configurations).
This allows guest to use host's internet connection (including VPN). But,
- You can't access guest from any network outside of host's internal network
- Virtualbox seems to have a glitch, where-in if host uses cisco VPN and the VPN is enabled, guest fails to access outside world

#### Solution

- For 1st problem, just set up [port forwarding](https://www.virtualbox.org/manual/ch06.html#natforward) using VboxManage.exe, like so:
  ```
  "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" modifyvm "VM name" --natpf1 "ssh,tcp,,8022,,22"
  ```

- For 2nd problem, [this works](https://community.cisco.com/t5/vpn-and-anyconnect/anyconnect-3-1-04072-won-t-allow-internet-connectivity-from/td-p/2400378):
  ```
  "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" modifyvm "VM name" --natdnshostresolver1 on
  ```

> Guest needs to be powered off before exercising the commands mentioned above.
