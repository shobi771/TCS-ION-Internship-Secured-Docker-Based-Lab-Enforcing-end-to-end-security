# TCS-ION-Internship-Secured-Docker-Based-Lab-Enforcing-end-to-end-security
TCS iON RIO-125: Secured Docker Based Lab: Enforcing end-to-end security for project environments. This repository contains the Docker configurations, security testing and compliant lab environments for secure development and testing.

**Introduction**
The objective of this project is to create a Docker-based lab environment incorporating multiple application stacks (Python, Java, Web Server, MySQL) with a private internal network and static IP addressing. The environment is then deployed on an AWS EC2 instance and subjected to various vulnerability scanning and penetration testing tools in order to identify and mitigate security risks.

**Phase 1: Lab Environment Creation Using Docker**
**1. Create Docker Network**

•	Login into the Docker.

•	Create Images of the project.

•	Private bridge network with a subnet, e.g. 172.20.0.0/16


**2. Environment Design & Architecture**

**2.1 Base Configuration**

**Component**                                                                              **Configuration**

Base OS Image                Ubuntu (latest)

Container Networking                                    	                             Custom Docker bridge network with subnet 172.20.0.0/16

Network Type                                      	                                     Private internal network

Deployment Orchestrator                                 	                             Docker Compose (for multi-container setup)

<img width="1195" height="632" alt="Docker-setup" src="https://github.com/user-attachments/assets/28221d63-c3e4-48cb-9d00-01e4d8a231a4" />



**Create Folder Structure**

Create a folder named docker-lab with the following structure:

docker-lab/
    |--- docker-compose.yml
    |--- webserver/
            |--- Dockerfile
            |--- index.html
    |--- python/
            |--- Dockerfile
            |--- app.py
    |java/
        |--- Dockerfile
        |--- MyApp.java

<img width="450" height="270" alt="Create-Folder-Structure" src="https://github.com/user-attachments/assets/6ce6112f-91da-41da-a6e5-a592eb6c3f9f" />


**2.2 Containers and Services**
Service	        Base Image	                Static IP	        Exposed Port	        Description
Python App	python:3.10-slim / Ubuntu	172.20.0.2	             5000	            Flask test app (or API placeholder)
Java Service	openjdk:11-jre	                172.20.0.3	             8080	            Simple Java server app
Web Server	nginx:latest / apache	        172.20.0.4	             80	                    Static webpage
MySQL Database	mysql:5.7 / mysql:8	        172.20.0.5	             3306	            MySQL DB with user-defined credentials

**2.3 Port Exposure Policy**
Only essential service ports are exposed to the external host:
•	Web Server: 80 → Exposed
•	Python App: 5000 → Exposed if needed
•	MySQL: 3306 → Exposed (for testing) with password authentication
•	Java: 8080 → Optional, can be internal-only


**3. Build & Deployment**
**3.1 Docker Compose Summary**
A Docker Compose file is created to define containers and assign static IPs. Sample network configuration like this:
networks:
  lab_net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.18.0.0/16
Each container block specifies networks + ipv4_address ensuring a deterministic IP for testing and scanning. Docker-compose file is mentioned in the repository.

**3.2 EC2 Deployment**
•	Launch EC2 instance (Ubuntu)
<img width="1678" height="669" alt="Create-instance-OSUbuntu-8gbStorage" src="https://github.com/user-attachments/assets/0aa4be6b-f188-4549-90b1-a7aa958c345b" />


•	Install Docker and Docker Compose
<img width="1468" height="352" alt="Docker_installed-Ubuntu" src="https://github.com/user-attachments/assets/0fb32584-46a7-42fc-9e04-6028045735ed" />
<img width="1824" height="304" alt="Install Docker Compose (latest version)" src="https://github.com/user-attachments/assets/37050961-2c7c-4afb-8a32-400a0f492a26" />



•	Transfer project folder to EC2
•	Run: docker-compose build, docker-compose up -d or docker-compose up --build -d
<img width="1291" height="369" alt="Create-images container-for-static-IP" src="https://github.com/user-attachments/assets/53414553-0414-4725-9677-7e4715ff4804" />


•	Validate container reachability from EC2 host

<img width="1437" height="465" alt="Docker-Images-running-on-EC2-instance" src="https://github.com/user-attachments/assets/b3dabb04-a0e8-4de9-86d0-b1d2305f5de1" />

________________________________________
**4. Vulnerability Assessment & Penetration Testing**

**Tools Used:**

**Tool**----------                                                                    **Purpose**

traceroute----------                                              Network path detection, routing investigation

nmap----------	                                                         Port scanning, service detection

metasploit----------	                                                 Advanced penetration testing framework

nessus----------	                                                         Vulnerability assessment, CVE detection

hydra/john----------	                                                 Brute-force password attacks (e.g., MySQL login)

________________________________________
**Sample Test Activities**
**1. Nmap reveals open ports and potential service banners**
Commands that I used.
1.	docker exec -it python_app apt update
2.	docker exec -it python_app apt install -y nmap
3.	docker exec -it python_app nmap -sP 172.20.0.0/24
   <img width="556" height="200" alt="Nmap-inside-one-container" src="https://github.com/user-attachments/assets/c1b276ed-8fb6-4f63-98a6-68394a01df41" />
   

This shows whether all containers are reachable at the Docker subnet level.

**Vulnerability Scanning using Nmap**
Nmap was used to perform a vulnerability scan of the Docker environment from the EC2 host. The -sV scan detected active services on ports 80 (Apache), 5000 (Flask), 8080 (Java HTTP server), and 1234 (MySQL). No additional ports were open, indicating that only necessary services are exposed. This scan confirms that the Docker environment is not exposing unnecessary services, thereby reducing the risk of external exploitation.
<img width="1187" height="661" alt="Nmap-port-scanning" src="https://github.com/user-attachments/assets/bbfd3e42-a14a-4681-8678-681033cc7ec0" />




**2. Metasploit used to test for MySQL default credential vulnerabilities**
<img width="1310" height="452" alt="Install Metasploit Framework" src="https://github.com/user-attachments/assets/85e82a48-9c40-4f71-9ebc-d4ed6f2fb6e3" />
Installation of Metasploit framwork in my EC2 Instance.


This will show you that those ports are open (just like Nmap did).
<img width="790" height="247" alt="image" src="https://github.com/user-attachments/assets/232ea949-aa14-477a-b620-c54957487e53" />

**Service & vulnerability scan module (example)**
Scan to fingerprint services via HTTP & similarly mysql:
<img width="540" height="144" alt="Vulnerability-scan-Metasploit" src="https://github.com/user-attachments/assets/7e8de9c0-5e3f-4b7b-be5b-00e7cde5d95e" />


Similarly scan the Flask or Java app:
<img width="844" height="225" alt="image" src="https://github.com/user-attachments/assets/90f8d7cc-f2df-4cb4-91eb-08949174c92c" />


What Actually Happened

Output shows two key points:

[-] Unable to connect: The connection was refused ...

[*] Bruteforce completed, 1 credential was successful.

[*] You can open a MySQL session with these credentials...

✔️ That means Metasploit did guess the username/password correctly — it found valid credentials (root / 1234), BUT the connection on port 1234 was refused because the MySQL container is probably not running on that port, or firewall/security group is blocking.
BUT… the line:

➜ [*] Bruteforce completed, 1 credential was successful.


This actually indicates a successful credential match from the scanner even if the connection failed afterwards.
“Using Metasploit’s auxiliary scanner/mysql/mysql_login module, we were able to successfully discover the root credentials (root:1234) of the MySQL service, demonstrating a real vulnerability if weak passwords are used.”
<img width="928" height="253" alt="Bruteforce-on-mysql-default-pass-Metasploit" src="https://github.com/user-attachments/assets/662efea6-04a9-414e-956d-f5aacfd76288" />


Why Connection Failed?
It might be one of these:
1.	MySQL service listening on a different port OR
2.	MySQL is not exposed to the internet (only internal)
3.	AWS security group blocked port 1234 (so connection refused)
✅ But for exploit demonstration, the main point that Metasploit found the credentials is enough.
________________________________________
✅ OPTIONAL — How to Fix & Fully Connect (if you want)
If you want a full exploit session to prove login:
1.	Ensure MySQL is mapped to 3306 instead of 1234, or keep 1234 but allow inbound TCP port 1234 in your security group.
2.	Try manual login with:
3.	mysql -h 3.108.252.224 -P 1234 -u root -p1234
“We attempted to connect using MySQL client with the stolen credentials but received connection refused. This proves that port 1234 is not reachable externally, indicating network-level security is in place.”

**Metasploit Scanning**
Using the Metasploit Framework, the auxiliary TCP port scanning module (scanner/portscan/tcp) confirmed the same open ports detected by Nmap. Further modules like mysql_version, http_version, and http_title were used to identify the services running on Docker containers. The scan verified that only required ports were exposed to the public. No direct vulnerabilities were found in the configured Docker containers, but the framework confirmed possible attack vectors if weak credentials or outdated versions are used.
<img width="540" height="144" alt="Vulnerability-scan-Metasploit" src="https://github.com/user-attachments/assets/efb210ad-91d2-4128-891c-b7e93af918bc" />



**3. Hydra attempted brute-force on MySQL and web login forms**
Security Weaknesses Found in Scanning
1.	Weak MySQL credentials (root:1234) → Hydra cracked it easily.
2.	Exposed MySQL to public network (0.0.0.0:3306) → accessible from anywhere.
3.	All services exposed to 0.0.0.0 (Python app, Java app, Apache webserver).
4.	No network segmentation → all containers can talk to each other.
5.	No encryption → MySQL traffic is plaintext, HTTP traffic is unencrypted.
6.	Containers running as root user by default → privilege escalation risk.
🔑 Summary of Enhancements
1.	✅ Strong passwords stored as Docker secrets
2.	✅ MySQL restricted to internal access only
3.	✅ Webserver upgraded to HTTPS
4.	✅ Containers run as non-root
5.	✅ Separate networks (frontend vs backend)
6.	✅ MySQL with SSL enabled
7.	✅ Resource limits added

**Hydra Password Crack Attempt**
We created a small dictionary of weak passwords and executed a Hydra attack on the MySQL service running on port 1234. Hydra successfully identified a valid login: username root with password rootpass. This demonstrates that services inside Docker containers can still be compromised if weak credentials are used.
<img width="1432" height="298" alt="hydra-bruteForce-on-Mysql-port-mapping" src="https://github.com/user-attachments/assets/e6685b50-90e9-439c-9aca-7802dcc33a9c" />
using of password list as mentioned:-

<img width="362" height="116" alt="Using-passlist-for-mysql-brutforce" src="https://github.com/user-attachments/assets/574a4cee-3d3d-4c2f-944a-a7c99f163ea7" />



•	Traceroute used between containers to inspect internal routing latency
installation of Traceroute:-
<img width="1261" height="485" alt="Install Traceroute" src="https://github.com/user-attachments/assets/d4935402-813a-45a9-9658-5b623b97250a" />

Traceroute connection checking for static IP-Address:-
<img width="648" height="191" alt="Traceroute-connection-check-static-IP" src="https://github.com/user-attachments/assets/d07f32ee-7c1a-42d7-b346-ca6fbd17b7bb" />



•	Nessus scan reveals insecure configurations, outdated software
________________________________________
**5. Post-Scan Hardening & Modifications**

Based on scan results, the environment is hardened:

•	Disabled remote root login for MySQL
Disabling remote login for MySQL can be achieved through several methods, primarily by configuring the MySQL server to only listen on the local interface and by managing user privileges.

1. Configure MySQL to Bind to Localhost:
   
The most effective way to disable remote connections is to configure the MySQL server to only accept connections from the local machine. This is done by modifying the my.cnf (or my.ini on Windows) configuration file.

Locate the configuration file:

The location varies depending on the operating system and installation method. Common locations include /etc/my.cnf, /etc/mysql/my.cnf, or within the MySQL installation directory.

Edit the bind-address setting:

Open the my.cnf file and locate the [mysqld] section. Add or modify the bind-address directive to 127.0.20.40

    [mysqld]
    bind-address = 127.0.0.1
    Restart MySQL: After saving the changes, restart the MySQL service for the changes to take effect.

2. Manage User Privileges:
   
While binding to localhost prevents external connections, it is also good practice to manage user privileges to explicitly restrict remote access.

Revoke remote access for specific users: Use the REVOKE command to remove privileges for users from specific hosts or all remote hosts.

Code

    REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'username'@'remote_host_ip';
    REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'username'@'%'; -- Revokes from all remote hosts
    
Create users with specific host restrictions: When creating new users, specify localhost or 127.0.0.1 as the host to limit their access to the local machine only.

Code

    CREATE USER 'local_user'@'localhost' IDENTIFIED BY 'password';
    
Flush privileges: After making changes to user privileges, execute FLUSH PRIVILEGES to reload the grant tables.

Code

    FLUSH PRIVILEGES;
    
4. Firewall Configuration (Optional but Recommended):
   
For an added layer of security, configure your server's firewall to block incoming connections to the MySQL port (default 3306) from external IP addresses, allowing only connections from localhost. Example using iptables (Linux).

Code

    sudo iptables -A INPUT -p tcp --dport 3306 -s 127.0.0.1 -j ACCEPT
    
    sudo iptables -A INPUT -p tcp --dport 3306 -j DROP
    
By combining these methods, you can effectively disable remote login to your MySQL server, enhancing its security posture.
    

•	Changed default passwords and enforced strong password policy

•	Closed unused ports (e.g., removed port 8080 from exposure)

•	Added firewall rules within Docker (iptables / ufw within containers)

•	Considered SSL/TLS for web server using self-signed certificates

________________________________________
6. Conclusion
The project successfully demonstrates the creation of a secure, containerized multi-service lab environment using Docker. Through penetration testing and vulnerability scanning tools, major weaknesses were identified and fixed. The environment can be further extended with encryption and container isolation best practices to meet production-grade security standards.


