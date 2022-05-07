# Host Discovery
  - ## Check list of hosts nmap will scan
    ```nmap -sL TARGETS```
    **Example using range of hosts**
    ```nmap -sL -n 10.10.0-255.101-125``` will give the below output

      ![](./images/nmap1.png)

   *This option will gives a detailed list of the hosts that Nmap will scan without scanning them*
  
  - ### Discover online hosts without port-scanning the live systems
    ```
    nmap -sn TARGETS
    ```
 
  - ## ARP Scanning
    - ARP scan is possible only if you are on the same subnet as the target systems. On an Ethernet (802.3) and WiFi (802.11), you need to know the MAC address of any system before you can communicate with it. 
    ```
    nmap -PR -sn TARGETS  
    ```
    *where -PR indicates that only ARP scan should be perform*
    
      ![](./images/arp.png)
    
  - ### arp-scan
    - ***This is the scanner built around ARP Queries<br> it provides many options to customize your scan.***
    ```
    apt install arp-scan
    ```
    ```
    arp-scan --help
    ```
    ![](./images/arp2.png)
    
  - ## ICMP 
  
   - #### ICMP Echo to discover live hosts
  ```
  nmap -PE -sn TARGET
  ```
   - #### ICMP Timestamp to discover live hosts
  ```
  nmap -PP -sn TARGET
  ```
   - #### ICMP Address Mask to discover live hosts
  ```
  nmap -PM -sn TARGET
  ```
 
  ## Using Reverse-DNS Lookup
   - Nmap’s default behaviour is to use reverse-DNS online hosts. Because the hostnames can reveal a lot, this can be a helpful step. However, if you don’t want to send such DNS queries, you use `-n` to skip this step.

   - By default, Nmap will look up online hosts; however, you can use the option `-R` to query the DNS server even for offline hosts. If you want to use a specific DNS server, you can add the `--dns-servers DNS_SERVER` option.

# Port Scanning
- ### Nmap port states
  - Open
  - Closed
  - Filtered
  - Unfiltered
  - Open|Filtered
  - Close|Filtered
  
 - ## TCP Connect Scan
    - Any open TCP port will require Nmap to complete the TCP 3-way handshake before closing the connection
      `nmap -sT TARGET`
 ![image](https://user-images.githubusercontent.com/16500435/167274350-a98bad48-d14e-49cd-93b4-e10fce492ca0.png)


    <br>scanning with `-F` enable fast mode and `-r` option scan the ports i consecutive order instead of random order.<br>
    ***Unprivileged users are limited to connect scan***
 
 - ## SYN Scan
     - SYN scan `-sS` does not need to complete the TCP 3-way handshake; instead, Nmap sends an RST packet once a SYN/ACK packet is received.
     `nmap -sS TARGET` <br>
      ***This requires privilege/sudo right***
![image](https://user-images.githubusercontent.com/16500435/167274547-bb79d386-a2dc-426b-8207-e73d63b531be.png)
   -TCP SYN scan is the default scan mode when running Nmap as a privileged user 

- ## UDP Scan
   - UDP is a connectionless protocol, and hence it does not require any handshake for connection establishment. We cannot guarantee that a service listening on a UDP port would respond to our packets. However, if a UDP packet is sent to a closed port, an ICMP port unreachable error (type 3, code 3) is returned. 
  `nmap -sU TARGET` <br>
  ![image](https://user-images.githubusercontent.com/16500435/167274813-cda90e06-2bc7-46c8-98d6-e81ba5494a71.png)
  
  ![image](https://user-images.githubusercontent.com/16500435/167274824-0bb70623-c083-41e7-8c57-0b0fe42724c3.png)
  
 -## Scan enhancement/fine-tunning
    - **Port list:** `-p22,80,443`
    - **Port range:** `-p1-1023` range is inclusive
    - **All 65535 ports:** `-p-`
    - **top 10 most common ports:** `--top-ports 10`
    - **Control scan timing:** `-T<0-5>` 
      - paranoid (0)
      - sneaky (1)
      - polite (2)
      - normal (3)
      - aggressive (4)
      - insane (5)
    - **control the packet rate:** `--min-rate <number>` and `--max-rate <number>` e.g `--max-rate 10` or `--max-rate=10`
    - **control probing parallelization:** `--min-parallelism <numprobes>` and `--max-parallelism <numprobes` e.g `--min-parallelism=512`
 -## Null Scan
    `nmap -sN TARGET`
   - The null scan does not set any flag; all six flag bits are set to zero.
   - A TCP packet with no flags set will not trigger any response when it reaches an open port, therefore, from Nmap’s perspective, a lack of reply in a null scan indicates that either the port is open or a firewall is blocking the packet. 
   ![image](https://user-images.githubusercontent.com/16500435/167275630-b8ee1d11-1b0d-4d45-bd1d-8a06f4ed8479.png)
   
   - <br>However, we expect the target server to respond with an RST packet if the port is closed.
   ![image](https://user-images.githubusercontent.com/16500435/167275653-877787bb-3001-4223-b74c-9e8ff91dc007.png)
   
 - ## FIN Scan
    `nmap -sF TARGET`
    - The FIN scan sends a TCP packet with the FIN flag set. 
    - no response will be sent if the TCP port is open.
  ![image](https://user-images.githubusercontent.com/16500435/167275697-5774b52a-c208-4aab-b39f-f32263d7d0db.png)

   - target system should respond with an RST if the port is closed
  ![image](https://user-images.githubusercontent.com/16500435/167275702-82c24165-f338-484f-8f88-19eb3f7365fa.png)

 - ## Xmas Scan
    `nmap -sX TARGET`
    - An Xmas scan sets the FIN, PSH, and URG flags simultaneously
    -  if an RST packet is received, it means that the port is closed. Otherwise, it will be reported as open|filtered.
    ![image](https://user-images.githubusercontent.com/16500435/167275728-75098f46-7959-4985-a678-d75a2c196e71.png)
    ![image](https://user-images.githubusercontent.com/16500435/167275750-4f5b22e0-9322-4d14-b1e4-344fcde38bae.png)


    
    
# References and Reading
  - [TryHackMe Nmap Live Host Discovery Room](https://tryhackme.com/room/nmap01)
  -  [THM Nmap Basic Port Scans Room](Nmap Basic Port Scans)
