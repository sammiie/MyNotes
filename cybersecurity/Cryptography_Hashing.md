# Calculating Files Hash value
  - # Windows OS
    - ## Using third party applications
      - Hashtab
        - ![](./images/hashtab1.png)
    - ## Powershell
      - **CertUtil command**
      
      ```
       CertUtil -hashfile .\TryHackMe.exe SHA256
       
       CertUtil -hashfile <filename> <algorithm>
       
      ```
      ![](./images/certutil.png)
      
      - **Get-FileHash command**

      ```
        Get-FileHash .\Wannacry.exe -Algorithm MD5

        Get-FileHash <filename> -Algorithm <algorithm>
       ```        
         ![](./images/gethash.png)
          
