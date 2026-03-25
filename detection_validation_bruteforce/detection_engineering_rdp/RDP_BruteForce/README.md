# Detection Engineering Lab: RDP Brute Force Simulation

Detection Engineering Lab: RDP Brute Force Simulation

## Objective

The goal of this simulation was to validate that a Wazuh-based SOC environment can detect brute force login attempts against a Windows system via Remote Desktop Protocol (RDP).

## Environment

-- **Attacker:** Kali Linux – 192.168.100.11  
- **Target:** Windows Server 2022 – 192.168.100.20  
- **SIEM:** Wazuh Server – 192.168.100.10  
- Tools used: xfreerdp

## Attack Execution

The attack was simulated using repeated failed RDP login attempts from the attacker machine.
This command was executed multiple times (5–10 attempts) to simulate brute force behavior by triggering repeated authentication failures.

'''bash
xfreerdp /v:192.168.100.20 /u:Administrator /p:WrongPassword /cert:ignore
Repeated 5–10 times to generate failed login attempts.

![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image.png.BaseName)](screenshots/image.png)

## Detection Results

The attack generated multiple failed login events, which were successfully captured by Wazuh.

Windows Event ID 4625 (Failed Logon) was observed
Source IP: 192.168.100.11 (Kali attacker)
Target system: Windows Server VM
Multiple authentication failures detected within a short time frame

Windows Event ID 4625 indicates a failed login attempt.

The logs were filtered to display Event ID 4625, revealing multiple failed login attempts.

Drilling into a single event shows detailed information about the failed login attempt.

Agent Name: Windows VM (monitored endpoint)
Event: Failed logon attempt

Further inspection shows:

System name: win22-vm
Message: "An account failed to log on"
Source IP: 192.168.100.11
Destination IP: Windows Server VM


- Event ID 4625 logged in Wazuh
- Source IP: 192.168.100.11
- Failed login attempts detected as expected
- Screenshots attached (Wazuh logs)

Windows Event ID 4625 = Failed Logon

![image.png](screenshots/image1.png)

Filtered logs for Event ID 4625 showing multiple failed login attempts

![image.png](screenshots/image2.png)

Clicking into one of the events, showing details. The ‘agent name’ is our windows vm. The agent id is our windows vm IP that we assigned to that vm.

![image.png](screenshots/image3.png)

Scrolling down, showing the computer is win22-vm, which is what we renamed the windows vm. Also then showing ‘An account failed to log in’, and showing the source IP address, and the destination IP address.

![image.png](screenshots/image4.png)

Showing the pattern, the several attempts we made on the kali vm, are showed here in the screenshot, showing the failed logon attempts, coming from the same ip address over and over.

![image.png](screenshots/image5.png)

## Analysis

Analysis

Initial attempts to simulate the attack using tools such as Hydra and CrackMapExec were unsuccessful due to instability and protocol limitations in the lab environment.

To ensure reliable log generation, repeated failed RDP authentication attempts were manually executed using xfreerdp. This approach successfully simulated brute force behavior and generated the required telemetry.

Wazuh effectively:

Collected Windows Security Event logs
Detected repeated authentication failures
Allowed correlation of activity to the attacker’s IP address

## Conclusion

Conclusion

This simulation confirms that the Wazuh detection pipeline is functioning correctly and is capable of identifying brute force login attempts against RDP services.

Repeated failed authentication attempts were successfully logged, detected, and analyzed, demonstrating the effectiveness of the configured monitoring environment.

Key Takeaways
Event ID 4625 is critical for detecting brute force attempts
Repeated failed logins from a single source are a strong attack indicator
Detection validation is just as important as detection configuration
Tool instability can occur, and adapting methodology is part of real-world workflows

## Screenshots

1. Terminal output of xfreerdp
2. Wazuh event list (4625)
3. Detailed event showing source IP
4. Repeated pattern
![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image.png.BaseName)](screenshots/image.png)
![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image1.png.BaseName)](screenshots/image1.png)
![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image2.png.BaseName)](screenshots/image2.png)
![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image3.png.BaseName)](screenshots/image3.png)
![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image4.png.BaseName)](screenshots/image4.png)
![$(C:\Users\5c4rf\Projects\Write_Ups\detection_validation_bruteforce\detection_engineering_rdp\RDP_BruteForce\screenshots\image5.png.BaseName)](screenshots/image5.png)