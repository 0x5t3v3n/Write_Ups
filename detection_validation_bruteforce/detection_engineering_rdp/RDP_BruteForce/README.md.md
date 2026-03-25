# Detection Engineering Lab: RDP Brute Force Simulation

Detection Engineering Lab: RDP Brute Force Simulation

## Objective

Validate Wazuh detection of brute force attacks via RDP.

## Environment

- Kali Linux attacker VM: 192.168.100.11
- Windows Server 2022 victim VM: 192.168.100.20
- Wazuh Server: 192.168.100.10
- Tools used: xfreerdp

## Attack Execution

xfreerdp /v:192.168.100.20 /u:Administrator /p:WrongPassword /cert:ignore
Repeated 5–10 times to generate failed login attempts.

![image.png](image.png)

## Detection Results

- Event ID 4625 logged in Wazuh
- Source IP: 192.168.100.30
- Failed login attempts detected as expected
- Screenshots attached (Wazuh logs)

Windows Event ID 4625 = Failed Logon

![image.png](image%201.png)

Filtered logs for Event ID 4625 showing multiple failed login attempts

![image.png](image%202.png)

Clicking into one of the events, showing details. The ‘agent name’ is our windows vm. The agent id is our windows vm IP that we assigned to that vm.

![image.png](image%203.png)

Scrolling down, showing the computer is win22-vm, which is what we renamed the windows vm. Also then showing ‘An account failed to log in’, and showing the source IP address, and the destination IP address.

![image.png](image%204.png)

Showing the pattern, the several attempts we made on the kali vm, are showed here in the screenshot, showing the failed logon attempts, coming from the same ip address over and over.

![image.png](image%205.png)

## Analysis

- Initial attempts with Hydra and CrackMapExec failed due to tool instability.
- Using xfreerdp allowed reliable log generation.
- Wazuh successfully captured and reported repeated authentication failures.

## Conclusion

Detection pipeline is validated. Brute force attempts via RDP can be reliably identified.

## Screenshots

1. Terminal output of xfreerdp
2. Wazuh event list (4625)
3. Detailed event showing source IP
4. Repeated pattern