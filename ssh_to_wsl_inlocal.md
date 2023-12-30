## Accessing a WSL 2 distribution from your local area network (LAN)

**Step 1: On PowerShell - Windows**
- Links: https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell

- Install OpenSSH on Window. 
    -  Part *"Install OpenSSH for Windows"*
- Connect to OpenSSH Server Window
    - Part *"Connect to OpenSSH Server"*
    - Check detail connect on Windows/WSL, using the right terminal
        ```
        whoami
        > shanserver1\sonnm
        ```
    - Connect using the line below
        ```
        ssh domain\username@servername

        E.g:
        ssh shanserver1\sonnm@<IP of the Machine>
        ```
    
- If 
**Step 2: On Bash Terminal - WSL**

- Install OpenSSH in WSL Ubuntu.
    - Links: https://ubuntu.com/server/docs/service-openssh
- Run ssh service
    ```
    sudo service ssh start
    ```
- Check the service run properly on port 22
    ```
    sudo service ssh status

    >  ssh.service - OpenBSD Secure Shell server
        Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
        Active: active (running) since Sat 2023-12-30 14:56:29 +07; 54s ago
        Docs: man:sshd(8)
                man:sshd_config(5)
    Main PID: 1468 (sshd)
        Tasks: 1 (limit: 38514)
        Memory: 1.7M
        CGroup: /system.slice/ssh.service
                └─1468 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

        Dec 30 14:56:29 SHANSERVER1 systemd[1]: Starting OpenBSD Secure Shell server...
        Dec 30 14:56:29 SHANSERVER1 sshd[1468]: Server listening on 0.0.0.0 port 22.
        Dec 30 14:56:29 SHANSERVER1 sshd[1468]: Server listening on :: port 22.
        Dec 30 14:56:29 SHANSERVER1 systemd[1]: Started OpenBSD Secure Shell server.
    ```

**Step 3: Open Firewall on Windows**
- Allow port for remote from other machine.
    - Raw
    ```
    netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=<host_port_number>
    ```
    - Example: Allow opens port 2222 from other machine
    ```
    netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=2222
    ```

- Port forwarding
    - Raw
    ```
    netsh interface portproxy add v4tov4 listenport=<host_port_number> listenaddress=0.0.0.0 connectport=<wsl_sshService_port_number> connectaddress=(wsl_IP)
    ```

    - ```0.0.0.0``` means this ```host_port_number``` (E.g: 2222) are allowed to listen from any machines in local network.
    - ```wsl_IP``` is the IP of the WSL. This is an output of ```wsl hostname -I``` run in PowerShell OR ```ip a``` in the Bash terminal.
    - By default, ```wsl_sshService_port_number``` is 22

    - Example
    ```
    netsh interface portproxy add v4tov4 listenport=2222 listenaddress=0.0.0.0 connectport=22 connectaddress=172.25.177.228
    ```

**Step 4: SSH from Client WSL to Host WSL**
- Open Bash terminal on Host, using ```whoami``` to know the destination server name ```<server_name>```
    - Example
        ```
        whoami
        > shanserver1
        ```
- Find ```<host_IP>``` by using ```ipconfig``` in PowerShell or Windows Terminal
- From Client Bash terminal, using
    ```
    ssh <server_name>@<host_IP>
    ```
- Password is the sudo password of host WSL


**Step 5: Tunneling from VS Code**
- Choose ```Connect to Host```
- Using `host_IP`, `server_name` and password to connect

Then have fun!