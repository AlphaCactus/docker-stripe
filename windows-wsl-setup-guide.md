# Windows 10 + WSL2 Setup Guide
- (Optional) Install PowerShell v7
- Open PowerShell.
- `wsl --install -d Ubuntu`
- Check wsl version: `wsl -l -v`
- If version 1, set version 2 with `wsl --set-version Ubuntu 2` then check version agian.
   - Run `wsl` from PowerShell at any time to connect to a WSL shell.
   - Once installed, a new orange `Ubuntu` icon will be available in the start menu for quick shell access.
- (Optional) Install ssh key and configure putty to connect to WSL
   - See instructions below.
   - Useful to run [dbdump/zget_recent_db.sh](dbdump/zget_recent_db.sh) which will require an SSH key to authenticate. 

## DNS Server Re-assignment
According to [local_dns/README.md](local_dns/README.md), the DNS server for your current connection must be set to 127.0.0.1

### Simple solution
- Right click network icon in system tray
- Select Open Network and Internet settings
- Select Change adapter options
- Select your active connection (wifi or ethernet), right click and choose "Properties"
- Select "Internet Protocol Version 4 (TCP/IPv4) and Properties
- Select General - Use the folliwng DNS server addresses.
- Enter `127.0.0.1`
- Click OK

DNS will not work unless the docker container is running.

### Simple Solution scripted
- Determine the InterfaceIndex of your active network device
   - `Get-NetAdapter | Select-Object InterfaceAlias , InterfaceIndex | Where-Object {$_.InterfaceAlias -like "*Wi-Fi*" }`
- Same command with no filter
   - `Get-NetAdapter | Select-Object InterfaceAlias , InterfaceIndex`
- Use the InterfaceIndex instead of `##` in the next command to use the docker BIND container as local DNS service.
   - `Set-DnsClientServerAddress -InterfaceIndex ## -ServerAddresses ("127.0.0.1","127.0.0.1")`
- If the `local_dns` container is not running, revert to default DNS settings provded by router with
   - `Set-DnsClientServerAddress -InterfaceIndex ## -ResetServerAddresses`

## SSL Access to Ubuntu on WSL2
This is not necessary to use docker and docker containers.
Also, using `wsl` command from PowerShell provides quick shell access. However, adding this functionality adds the
ability to connect to Ubuntu and use it as an Ubuntu virtual machine to run linux commands.
- Open PowerShell (or use existing session)
- `sudo apt-get install openssh-server`
- `sudo service ssh start`
- Connect with putty. `127.0.0.1` using user/password used to setup WSL.
- `mkdir ~/.ssh`
- `chmod 700 .ssh`
- `touch ~/.ssh/authorized_keys`
- `chmod 600 ~/.ssh/authorized_keys`
- `nano ~/.ssh/authorized_keys`
- Paste in public key used by putty.
- `sudo nano /etc/ssh/sshd_config`
- Uncomment `AllowAgentForwarding yes`
- Save
- Restart ssh `sudo service ssh restart`
- Create a putty session configuration
   - Host Name  (or IP address): `127.0.0.1`
   - Connection->Data->Auto-login username: `dev`
   - Connecttion->SSH->Auth: Allow agent forwading (checked)
- Close shell and start new session.
- Check agent fordwarding: `ssh -T git@github.com` should return "Hi <Username!>", not a public-key error.
- Connect to Ubuntu shell.
- Check Ubuntu version: `lsb_release -a`
- Update Ubuntu `sudo apt update && sudo apt upgrade`
- Install unzip `sudo apt-get install unzip`
   - used by [dbdump/zget_recent_db.sh](dbdump/zget_recent_db.sh) 
