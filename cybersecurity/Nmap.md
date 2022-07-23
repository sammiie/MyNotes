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
      ```
      nmap -sT TARGET
      ```
      
 ![image](https://user-images.githubusercontent.com/16500435/167274350-a98bad48-d14e-49cd-93b4-e10fce492ca0.png)


   <br>scanning with `-F` enable fast mode and `-r` option scan the ports i consecutive order instead of random order.<br>
    ***Unprivileged users are limited to connect scan***
 
 - ## SYN Scan
     - SYN scan `-sS` does not need to complete the TCP 3-way handshake; instead, Nmap sends an RST packet once a SYN/ACK packet is received.
     ```
     nmap -sS TARGET
     ```
      ***This requires privilege/sudo right***
![image](https://user-images.githubusercontent.com/16500435/167274547-bb79d386-a2dc-426b-8207-e73d63b531be.png)
   -TCP SYN scan is the default scan mode when running Nmap as a privileged user 

- ## UDP Scan
   - UDP is a connectionless protocol, and hence it does not require any handshake for connection establishment. We cannot guarantee that a service listening on a UDP port would respond to our packets. However, if a UDP packet is sent to a closed port, an ICMP port unreachable error (type 3, code 3) is returned. 
  ```
  nmap -sU TARGET  
  ```
  
  ![image](https://user-images.githubusercontent.com/16500435/167274813-cda90e06-2bc7-46c8-98d6-e81ba5494a71.png)
  
  ![image](https://user-images.githubusercontent.com/16500435/167274824-0bb70623-c083-41e7-8c57-0b0fe42724c3.png)
  
 - ## Scan enhancement/fine-tunning
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
    
 - ## Null Scan
    ```
    nmap -sN TARGET
    ```
    
   - The null scan does not set any flag; all six flag bits are set to zero.
   - A TCP packet with no flags set will not trigger any response when it reaches an open port, therefore, from Nmap’s perspective, a lack of reply in a null scan indicates that either the port is open or a firewall is blocking the packet. 
   ![image](https://user-images.githubusercontent.com/16500435/167275630-b8ee1d11-1b0d-4d45-bd1d-8a06f4ed8479.png)
   
   - <br>However, we expect the target server to respond with an RST packet if the port is closed.
   ![image](https://user-images.githubusercontent.com/16500435/167275653-877787bb-3001-4223-b74c-9e8ff91dc007.png)
   
 - ## FIN Scan
    ```
    nmap -sF TARGET
    ```
    
    - The FIN scan sends a TCP packet with the FIN flag set. 
    - no response will be sent if the TCP port is open.
  ![image](https://user-images.githubusercontent.com/16500435/167275697-5774b52a-c208-4aab-b39f-f32263d7d0db.png)

   - target system should respond with an RST if the port is closed
  ![image](https://user-images.githubusercontent.com/16500435/167275702-82c24165-f338-484f-8f88-19eb3f7365fa.png)

 - ## Xmas Scan
    ```
    nmap -sX TARGET
    ```
    
    - An Xmas scan sets the FIN, PSH, and URG flags simultaneously
    -  if an RST packet is received, it means that the port is closed. Otherwise, it will be reported as open|filtered.
    ![image](https://user-images.githubusercontent.com/16500435/167275728-75098f46-7959-4985-a678-d75a2c196e71.png)
    ![image](https://user-images.githubusercontent.com/16500435/167275750-4f5b22e0-9322-4d14-b1e4-344fcde38bae.png)

  - ## Maimon Scan
      ```
      nmap -sM TARGET
      ```
      - the FIN and ACK bits are set. 
      - The target should send an RST packet as a response. However, certain BSD-derived systems drop the packet if it is an open port exposing the open ports.
      - **Most target systems respond with an RST packet regardless of whether the TCP port is open. In such a case, we won’t be able to discover the open ports.**
      -
      
      ![image](https://user-images.githubusercontent.com/16500435/167275983-05ddf66c-ef9c-4b46-872a-a1e43e05e86d.png)
      
      - The console output below is an example of a TCP Maimon scan against a Linux server. As mentioned, because open ports and closed ports are behaving the same way, the Maimon scan could not discover any open ports on the target system.
      -
      ![image](https://user-images.githubusercontent.com/16500435/167276030-8623345e-aad7-4a17-8e69-c23c4a8ef5ff.png)


 - ## TCP ACK Scan
  ```
  nmap -sA TARGET
  ```
   - the target would respond to the ACK with RST regardless of the state of the port.
   - Hence, this scan won’t tell us whether the target port is open in a simple setup.
   ![image](https://user-images.githubusercontent.com/16500435/167276134-1b1fce9c-a401-4e14-8df2-9e0c967f963c.png)

   - This kind of scan would be helpful if there is a firewall in front of the target. 
   - Consequently, based on which ACK packets resulted in responses, you will learn which ports were not blocked by the firewall. ***In other words, this type of scan is more suitable to discover firewall rule sets and configuration***<br>
   - ***it is essential to note that the ACK scan and the window scan were very efficient at helping us map out the firewall rules. However, it is vital to remember that just because a firewall is not blocking a specific port, it does not necessarily mean that a service is listening on that port.***
 
 - ## Window Scan
    ```
    nmap -sW TARGET
    ```
    - The TCP window scan is almost the same as the ACK scan; however, it examines the TCP Window field of the RST packets returned. 
    
    ![image](https://user-images.githubusercontent.com/16500435/167276447-5ccb92f2-870c-47e0-a848-fba0f511a8d9.png)

    - Launching a TCP window scan against a Linux system with no firewall will not provide much information
    - the console output shown below, the TCP window scan pointed that three ports are detected as closed. 
    - Although we know that these three ports are not closed, we realize they responded differently, indicating that the firewall does not block them.
    
    ![image](https://user-images.githubusercontent.com/16500435/167276474-dbe74221-3342-4331-9312-985e215a6c7a.png)
 
 - ## Custom Scan
    ```
    nmap --scanflags RSTSYNFIN
    ```
    
    - we can experiment with a new TCP flag combination beyond the built-in TCP scan types, you can do so using `--scanflags`
      ![image](https://user-images.githubusercontent.com/16500435/167276522-5c1849d1-116a-4fa5-bb31-3d9581675cf1.png)


  - ## Spoofing and Decoys
      - In some network setups, you will be able to scan a target system using a spoofed IP address and even a spoofed MAC address. Such a scan is only beneficial in a situation where you can guarantee to capture the response.
      ```
      nmap -S SPOOFED_IP TARGET
      ```
      - Nmap will craft all the packets using the provided source IP address `SPOOFED_IP`
      - The target machine will respond to the incoming packets sending the replies to the destination IP address `SPOOFED_IP`
      ![image](https://user-images.githubusercontent.com/16500435/167276936-d9076f00-949e-4e5b-a5dc-9aaae6e454ae.png)

      - In brief, scanning with a spoofed IP address is three steps:
        - Attacker sends a packet with a spoofed source IP address to the target machine.
        - Target machine replies to the spoofed IP address as the destination.
        - Attacker captures the replies to figure out open ports
      ```
      nmap --spoof-mac SPOOFED_MAC TARGET
      ```
      - When you are on the same subnet as the target machine, you would be able to spoof your MAC address as well.
      
      - **attacker might resort to using decoys to make it more challenging to be pinpointed by make the scan appears to be coming from many IP addresses so that the attacker’s IP address would be lost among them.**
      
      ```
      nmap -D DECOY1, DECOY2, ME TARGET
      ```
      ![image](https://user-images.githubusercontent.com/16500435/167277041-b5a6d261-ac56-4da9-a4a8-12d464c436d9.png)

      - You can launch a decoy scan by specifying a specific or random IP address after -D. For example, `nmap -D 10.10.0.1,10.10.0.2,ME 10.10.161.106` will make the scan of 10.10.161.106 appear as coming from the IP addresses 10.10.0.1, 10.10.0.2, and then `ME` to indicate that your IP address should appear in the third order. Another example command would be `nmap -D 10.10.0.1,10.10.0.2,RND,RND,ME 10.10.161.106` , where the third and fourth source IP addresses are assigned randomly, while the fifth source is going to be the attacker’s IP address. In other words, each time you execute the latter command, you would expect two new random IP addresses to be the third and fourth decoy sources.


    
# References and Reading
  - [TryHackMe Nmap Live Host Discovery Room](https://tryhackme.com/room/nmap01)
  -  [THM Nmap Basic Port Scans Room](Nmap Basic Port Scans)
  -  [Well Known Ports](http://www.vmaxx.net/techinfo/ports.htm)
