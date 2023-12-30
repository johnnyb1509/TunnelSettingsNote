## Steps to forwarding service that running on WSL to access by other computers in the same network

- Step 1: Install WSLHostPatcher at https://github.com/CzBiX/WSLHostPatcher and put it in .profile
- Step 2: Put files in somewhere, such as ```C:\tools\.``` then add this line into your ```~/.profile``` in WSL2: 

```/mnt/c/tools/WSLHostPatcher.exe```. 

Then restart system.

- Step 3: Open Port to access by other computer in the same networks. Using PowerShell with Admin  ```netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=<port>```
- Step 4: Add inbound Rule in Firewall setting
- Step 6: Find IP of wsl by using ```ip a```
- Step 5: Using PowerShell with Admin ```netsh interface portproxy add v4tov4 listenport=8501 listenaddress=0.0.0.0 connectport=<desired port on WSL> connectaddress=<IP of WSL>```