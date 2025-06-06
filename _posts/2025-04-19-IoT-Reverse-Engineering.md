# WESECUU IoT Security Light Bulb System Security Review through Reverse Engineering

## Research Goals
&nbsp; For this research project, three objectives were devised that would highlight the overall security posture of the IoT device. These objectives cover reverse engineering of the IoT device from an external and internal hardware perspective. The objectives are as follows:
1. To assess the network protocols and API interactions of the WEESECUU Light Bulb Security Camera.
2. To assess the firmware security of the Anyka AK3918AV100 in comparison to its predecessor the Anyka AK3918EV300+ 32-bit ARM-based CPU.
3. To measure the security of the WESECUU Light Bulb Security Camera in comparison to trends regarding IoT security.

## Approach/Research Method
&nbsp; During this project, an outside-in experimental approach was taken to reverse engineer the IoT device. This method was appropriate and effective for this research project because of the IoT device form factor and volatility. It was apparent that this project would require the disassembly of the device and handling of the Primary Control Board (PCB) for chipset analysis and eventual firmware extraction. Due to the micro size of IoT devices, they are susceptible to accidental damage and bricking. For this approach, the device had to be reverse engineered externally before internally to avoid project delays from a damaged device.

## Implementation
&nbsp; To implement a logical approach to this project, this project started with nonvolatile testing of external facing services for the IoT device, including network analysis comprised of two areas: Bluetooth and Wi-Fi. Subsequently, a copy of the EseeCloud applications .apk file was obtained from apkpure to commence static and dynamic source code analysis. Afterwards, firmware was extracted from the device to perform manual and automated analysis. A further look into the implementation process for each section is in subsequent sections. For the results of these implementations, all findings are in the process of responsible disclosure to the manufacturer.

## Networking Analysis
### Bluetooth (BLE)
&nbsp; Although an unfamiliar topic, research into BLE security testing provided ample information regarding the development of test cases. These test cases involved the examination of BLE and MQTT traffic within Wireshark, and the ability to connect to the IoT device’s BLE signal. Performing these test cases would require new tools such as nRF sniffers and external capture plugins for Wireshark. An nRF sniffer is a firmware programmed onto a Development Kit (DK) or USB dongle like the ADAFruit BLE Sniffer used in this project. Besides the external capture plugins, Python 3 was a prerequisite to enable the capturing of BLE traffic on Wireshark’s COM3 interface. Lastly, the nRF Connect IOS or Android application was acquired and integrated into my testing.

### Wireless Fidelity (Wi-Fi)
&nbsp; To begin searching for vulnerabilities in the IoT’s Wi-Fi implementation, the device had to be examined from two vantage points: local hotspot and wireless router. The device had to be tested from an unconfigured and configured state as certain hardware was enabled and disabled. For example, the unconfigured device broadcasts its hotspot labeled IPCE800C80A18572 with a password of 11111111 but does not when configured. This configured state is an enabling act for Wi-Fi connectivity to a Wi-Fi routing device within the EseeCloud application.

### Android Application Analysis
&nbsp; While the EseeCloud application used in this project was an iOS application, an Android Application Package (APK) was reverse engineered as it was easily accessible via apkpure. Apkpure provided the .apk file at version 3.9.6.8, as indicated in the file name EseeCloud\(IP\ Pro,\ VR\ Cam\)_3.9.6.8_APKPure.apk. The apk file could not be analyzed in its current state so further tools needed to be implemented. These tools were defined as apktool, dex2jar, and APKHunt. Before obtaining further tools or performing actions, goals were developed for this testing. These goals were defined as the ability to manually and automatically analyze the .apk file along with capturing and deciphering web traffic of the iOS application in Burp Suite. Understanding the underlying source code would provide insight into the responses and requests processed by Burp Suite’s proxy.

## Firmware Extraction and Analysis
&nbsp; Overall, the extraction of the device’s firmware proved challenging for this project despite the Universal Asynchronous Receiver / Transmitter (UART) through holes that the PCB supplied. On first inspection of the device’s board, the primary goal of firmware extraction was going to be via the ground (GND), receiver (RX), and transmitter (TX) with a Universal Serial Bus (USB) to 3pin Transistor-Transistor Logic (TTL) serial converter. The wire ends of the serial convertor did not provide enough contact and soldering them would prevent the device from closing to factory specifications, so a Japan Solderless Terminal (JST) SH 1.0mm pitch 3pin micro header was soldered to the board and plugged into with a JST to serial converter.
For verification of a solid solder and U-Boot menu connection, it was determined that the required software for interfacing with the UART connection would be tested with PuTTY at 115200 bits per second, 8 data bits, no parity, 1 stop bit, and no flow control. Regrettably, the U-boot menu was inaccessible via PuTTY and Picocom on Linux; assumedly from a configuration of CONFIG_BOOTDELAY=-2 which prevented any interruption of the booting process. To overcome this obstacle, a HydraBus and Small Outline Integrated Circuit 8pin (SOIC-8) clip were acquired to extract firmware from the XMC-SPI NOR flash chip (XM25QH256C) with Flashrom in serprog mode. Once the firmware was extracted, manual and automatic analysis occurred with Binwalk, Ghidra, Open Firmware Reverse Analysis Konsole (OFRAK), and the Embedded Analysis (EMBA) Toolkit.

![20240628_011910157_iOS](https://github.com/user-attachments/assets/86e74cfd-162c-4c8a-872c-07ede7890163)

![U-Boot](https://github.com/user-attachments/assets/a514be17-78bd-49d3-ba00-0ba6f3caeaba)

![DO_on_FLASH](https://github.com/user-attachments/assets/f867b432-4a72-47d8-abe8-c0d48513909e)

![20240701_224357678_iOS](https://github.com/user-attachments/assets/64a580e9-258a-49f0-ab39-072793fea98e)

![20240701_223930784_iOS](https://github.com/user-attachments/assets/80599e9d-9435-42f7-98bb-9efd15a75003)

![Extraction1](https://github.com/user-attachments/assets/9aca96c2-a7f4-4c21-9b87-6bf153679c94)

![Extraction2](https://github.com/user-attachments/assets/26430839-a5ba-46bb-8f00-81a06c89f134)

![extracted_files](https://github.com/user-attachments/assets/2d0bd0a9-7b63-4bc0-bf4a-750db792f873)


## Results
&nbsp; In this section, the results of reverse engineering the IoT device’s networking protocols, android application, and firmware will transpire. After successful implementation and careful testing, it is determined that the device is susceptible to command execution, certificate attacks, Man-in-the-Middle attacks, web attacks, and various other categories of attacks that result from insecure coding practices.

### Networking Protocols

#### Bluetooth (BLE)
&nbsp; Initial inspection of BLE traffic on the COM3 interface within Wireshark provided limited information for further testing. There is no Message Queuing Telemetry Transport (MQTT) traffic passed, indicating that the IoT device does not employ the protocol. An attempt to connect to the device (IPCE800C80A1_8572) through the nRF Connect IOS application was made, but it refused any BLE connections (Appendix A). This result suggested that the device was looking for a unique advertisement that only the EseeCloud application emitted, as Wireshark showed it was scanning all the BLE advertising devices within the limited 80-meter indoor range (Appendix A). If so, the device would be vulnerable to spoofed BLE advertising.

![BLE_WireShark](https://github.com/user-attachments/assets/1e0ab59d-38cc-459d-b820-911f4a8ee026)

![20240608_194220000_iOS](https://github.com/user-attachments/assets/9f756aa6-3d64-424c-99ef-3e63759d8f88)

![Websocket_Wireshark2](https://github.com/user-attachments/assets/6a86a761-6302-4777-a291-99a282f16192)

#### Wireless Fidelity (Wi-Fi)
&nbsp; Appendix A references the results of this section. Upon connecting to the device’s hotspot, it was evident the device assigned itself as the default gateway, accessible at 172.14.10.1, and an internal IP address of 172.14.10.2 to the smartphone. After conducting a Nmap scan of the device from both vantage points, there was no difference in the accessible ports. Nmap provided two open ports for further testing: 80 and 1000. Unfortunately, the service on port 80 only contained an empty directory at URI /snapshot, while port 10000 did not work in a web browser, and Netcat prompted a forbidden HTTP access.
As a last resort for network analysis, the device’s network traffic was inspected to determine if it was secure through HTTPS. During an initial inspection, traffic was being missed from the IoT device to the router, so a middleman was set up by connecting the device and smartphone to an individual computer hotspot. This action led to the discovery of HTTP traffic containing secure nonce and accesskey information during the initial connection establishment from the EseeCloud application and the IoT device.

![InsecureHTTP_Wireshark - Copy](https://github.com/user-attachments/assets/9aaf1293-dbd5-41a5-9893-90bc9e3e9d5a)

![InsecureHTTPNone_Wireshark](https://github.com/user-attachments/assets/70396f14-7742-4510-baf8-53188e1a8020)

## Android Application
&nbsp; For this section, these referenced findings are shown as screenshots within Appendix C. After using apktool and dex2jar to decompile the APK file, dex2jar was nominated as the tool of preference for further analysis and testing. To save time, APKHunt was deployed before manually reviewing the code. APKHunt determined that EseeCloud’s APK file or the application contained over 30 results of potential vulnerabilities. Some of the vulnerabilities were exposed network configuration files, cleartext HTTP, hard-coded certificates, code execution, and various others. Alas, time constraints limited the ability to review all the potential vulnerabilities. Fortunately, the interception and inspection of Application Programming Interface (API) communication between the EseeCloud and iOS device indicated a Cross-Origin Resource Sharing (CORS) misconfiguration of the Access-Control-Allow-Origing parameter to a wildcard (*).
During manual source code analysis of the APK file, the following 19 vulnerabilities were under consideration:
1. Insecure Data Storage
2. Insecure Communication
3. SQL Injection
4. Improper Platform Usage
5. Improper Authentication
6. Use of Hardcoded Credentials
7. Improper Error Handling
8. Security Decisions via Untrusted Inputs
9. Client-Side Injection
10. Insecure Broadcast Receivers
11. Insecure File Permissions
12. Hardcoded Cryptographic Keys
13. Insecure Random Number Generators (RNG)
14. SSL/TLS Misconfigurations
15. Improper Session Handling
16. Deprecated or Unsafe APIs
17. Insecure Inter Process Communication (IPC)
18. Insecure Deserialization
19. Insecure or Outdated Libraries

![CMD_Execution](https://github.com/user-attachments/assets/e05982d5-2526-476a-ae5d-0a3bcae53662)

&nbsp; Out of 19 vulnerable categories, 11 were present within the application code, as shown in Appendix C. There were multiple instances of using SharedPreferences.Editor to store all types of data to include access_token, auth_code, KEY_USER_PWD, and other keys. No occurrences of the hardcoded client.execute() were found, but there are hardcoded strings such as “http://”. An instance of SQL injection for FtsTableInfo and readOptions was located, but there is no user input. There are over 835 instances of intent.putExtra where potentially sensitive data is transmitted, such as keys and tokens. Next, multiple instances of hardcoded credentials and an abundance of printStackTrace() results (2327) were found in the source code. There is no permission enforcement regarding intents and no processReceivedData(data). After searching the code, a total of 84 results for new Random() were found, and there is no code to trust all certificates. There are 15 results for editor.putString() that contain settings and configuration info, while others include Facebook Tokens. Furthermore, an instance of deserialization was observed on the bArr object.

## Firmware
&nbsp; In Appendix D, screenshots offer insight into the vulnerabilities and misconfigurations discovered during the manual and automatic reverse engineering of the IoT device’s firmware. For this section, the delivery of the results will start with the manual and end with the automatic approach.
Subsequently, Ghidra, and OFRAK were good starting points for string and function search with Python scripts. The base firmware did not provide vulnerable functions such as strcpy, gets, and puts. Also, a NOP sequence search and entropy analysis indicated no firmware obstructions through anti-reverse engineering tactics. To further enhance this statement, Machine Learning (ML) byte frequency analysis implied that the firmware binary was compressed based on the uniform byte frequency distribution with a peak at 0 and 255. A strings analysis in a binary decompiler did result in hardcoded credentials for an admin user account password, a devID, and developer email information. Other string searches included des, aes, encryption, and key but had no findings. To conduct further analysis, Binwalk extracted the embedded files while Jefferson and 7z extracted the .img files for a meaningful look into the file system and configuration files. For optimal insight into the binary files, ML K-Means clustering was performed on all binary files to determine the similar characteristics within distinct clusters. The /etc/passwd file contained a password hash for the root user, as there was no /etc/shadow file. Also, the /local folder contained rsa_private_key.pem and rsa_private_key1.pem files. Additionally, Jefferson extracted an onvif_config_1.xml that contained hardcoded credentials for the admin user and network configuration settings for RTSP and network locations.
Through automation, EMBA specified that the firmware binary file contained multiple vulnerable functions within 99 /etc/bin/ files like printf in wpa_cli, strcpy in anyka_ipc, and kernel modules. Besides the private keys indicated in the manual analysis of firmware files, EMBA found that the /bin/anyka_ipc binary contained an RSA private key through a string search. Also, it alerted 150 areas with weak permissions, one authentication issue, and multiple binary files lacking Relocation Read-Only (RELRO), No-Execute (NX), and Position Independent Executable (PIE). Due to these vulnerabilities, there were two post-execution opportunities with the /bin/wget and /sbin/telnetd binaries.
Based on the results, the objectives, as previously stated, were accomplished because the network protocols, APIs, and firmware security were successfully reverse engineered and assessed for comparison of the WESECUU IoT device to overall IoT security trends. To obtain an understanding of the IoT device’s overall security in comparison to industry trends, the results of this project were compared to OWASP Top Ten trends for IoT security as shown below:
I1 Weak Guessable, or Hardcoded Passwords
I2 Insecure Network Services
I3 Insecure Ecosystem Interfaces
I4 Lack of Secure Update Mechanism
I5 Use of Insecure or Outdated Components
I6 Insufficient Privacy Protection
I7 Insecure Data Transfer and Storage
I8 Lack of Device Management
I9 Insecure Default Settings
I10 Lack of Physical Hardening
&nbsp; In comparison to the results of this project, the IoT device failed sections I1, I2, I5, I6, I7, I8, and I9 of OWASP’s list. There were multiple indications of hardcoded passwords, insecure network utilization of HTTP, insecure components, insecure privacy protection, data storage, and default settings.

![JUAN_DEV](https://github.com/user-attachments/assets/2991fa8e-92e7-4fe1-89c0-ee1b2efd26d6)

![password_string](https://github.com/user-attachments/assets/0877444e-8922-4ea7-9ced-355f8791331a)
