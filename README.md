\# 🔐 PKI Certificate Lab



Welcome to my \*\*PKI-based authentication lab\*\* repository. This project demonstrates how to create a \*\*Certificate Authority (CA)\*\*, generate and sign \*\*server certificates\*\*, configure a \*\*PKI-based authentication system\*\*, and test \*\*secure communication\*\* using OpenSSL and browser certificate trust.



\---



\## 📚 Overview



This lab was completed to build hands-on experience with the core concepts of \*\*Public Key Infrastructure (PKI)\*\*, including:



\- \*\*Public-key encryption\*\*

\- \*\*Digital signatures\*\*

\- \*\*Certificate Authorities (CAs)\*\*

\- \*\*Certificate Signing Requests (CSRs)\*\*

\- \*\*Digital certificates\*\*

\- \*\*Browser trust\*\*

\- \*\*Secure communication using certificates\*\*



This repository includes the full setup process, screenshots, certificate files, and the findings paper.



\---



\## 🎯 Lab Objectives



By completing this project, I was able to:



\- Create a \*\*root Certificate Authority\*\*

\- Generate a \*\*server private key\*\*

\- Create a \*\*Certificate Signing Request (CSR)\*\*

\- Sign the server certificate with the CA

\- Configure hostname resolution for a test server

\- Import the CA certificate into Firefox

\- Demonstrate certificate-based trust

\- Test certificate tampering

\- Test hostname and address validation behavior



\---



\## 🛠️ Tools Used



\- \*\*OpenSSL\*\*

\- \*\*Windows Command Prompt\*\*

\- \*\*Mozilla Firefox\*\*

\- \*\*GitHub\*\*

\- \*\*Notepad\*\*

\- \*\*File Explorer\*\*



\---



\## 📁 Repository Structure



```text

PKI\_Lab\_EricSledge/

├── README.md

├── screenshots/

├── paper/

└── cert\_files/

📂 Folder Breakdown

screenshots/ → Contains all screenshots taken throughout the lab

paper/ → Contains the final findings paper

cert\_files/ → Contains PKI-related files created during the lab

📦 Files Included

cert\_files/

openssl.cnf

san.cnf

ca.crt

ca.key

ca.srl

server.crt

server.csr

server.key

server.pem

server\_backup.pem

demoCA/

paper/

PKI\_Findings\_EricSledge.docx

screenshots/



Contains screenshots documenting:



OpenSSL setup

CA creation

CSR generation

Certificate signing

Hosts file configuration

Browser trust warning

CA import into Firefox

Secure site loading

Certificate tampering failure

Hostname/address mismatch validation

🚀 Start-to-Finish Lab Instructions

1️⃣ Create the project folders



First, create a main folder for the lab and three subfolders:



C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge

C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\screenshots

C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\cert\_files

C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\paper



You can create them in Command Prompt with:



mkdir C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge

mkdir C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\screenshots

mkdir C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\cert\_files

mkdir C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\paper



Then move into the certificate working folder:



cd C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\cert\_files

2️⃣ Verify OpenSSL installation



Check whether OpenSSL is installed:



openssl version



If OpenSSL is not recognized, use the full executable path:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" version

3️⃣ Copy openssl.cnf into the working folder



The OpenSSL configuration file is needed for certificate operations. Copy it into the cert\_files folder.



Try one of these locations:



copy "C:\\Program Files\\Common Files\\SSL\\openssl.cnf" "C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\cert\_files\\openssl.cnf"



or



copy "C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.cnf" "C:\\Users\\ilsle\\Documents\\PKI\_Lab\_EricSledge\\cert\_files\\openssl.cnf"

4️⃣ Create the CA directory structure



OpenSSL needs a CA database structure. Create it with:



mkdir demoCA

mkdir demoCA\\certs

mkdir demoCA\\crl

mkdir demoCA\\newcerts

type nul > demoCA\\index.txt

echo 1000 > demoCA\\serial



This creates the required CA folders and files.



5️⃣ Create the root Certificate Authority (CA)



Generate a private key and self-signed CA certificate:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" req -new -x509 -keyout ca.key -out ca.crt -config openssl.cnf



When prompted, enter a password for ca.key. This password must be remembered because it will be needed later when signing the server certificate.



Example certificate details used:



Country Name: US

State: Georgia

Locality: Atlanta

Organization: Student PKI Lab

Organizational Unit: Cybersecurity

Common Name: Eric Sledge Root CA



This CA certificate becomes the trust anchor for the rest of the lab.



6️⃣ Generate the server private key



Create the server private key:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" genrsa -des3 -out server.key 2048



A password is also created for server.key. This password must be entered whenever the key is used.



7️⃣ Create the Certificate Signing Request (CSR)



Generate a CSR for the server:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" req -new -key server.key -out server.csr -config openssl.cnf



When asked for the certificate information, use:



Country Name: US

State: Georgia

Locality: Atlanta

Organization: Student PKI Lab

Organizational Unit: Cybersecurity

Common Name: PKILabServer.com



The Common Name is important because it identifies the server.



8️⃣ Create the SAN configuration file



Modern browsers require Subject Alternative Names (SANs) for hostname validation.



Create a file named san.cnf inside cert\_files with this content:



\[req]

distinguished\_name = req\_distinguished\_name

req\_extensions = req\_ext



\[req\_distinguished\_name]



\[req\_ext]

subjectAltName = @alt\_names



\[alt\_names]

DNS.1 = PKILabServer.com

DNS.2 = localhost

9️⃣ Generate a CSR using SAN configuration



Create a new CSR using the SAN config file:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" req -new -key server.key -out server.csr -config san.cnf -subj "/C=US/ST=Georgia/O=Student PKI Lab/OU=Cybersecurity/CN=PKILabServer.com"



This ensures that the certificate request includes the required hostname information.



🔟 Sign the server certificate with the CA



Sign the CSR and issue the server certificate:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365 -sha256 -extfile san.cnf -extensions req\_ext



This creates server.crt, signed by the CA.



1️⃣1️⃣ Verify the certificate contents



Check the server certificate details:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" x509 -in server.crt -text -noout



Check the CA certificate details:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" x509 -in ca.crt -text -noout



This confirms the certificate information and SAN entries.



1️⃣2️⃣ Build the PEM file



Combine the server private key and server certificate into one file for the OpenSSL test server:



copy /Y server.key server.pem

type server.crt >> server.pem



Also make a backup copy:



copy /Y server.pem server\_backup.pem

1️⃣3️⃣ Update the Windows hosts file



Open the Windows hosts file as Administrator:



C:\\Windows\\System32\\drivers\\etc\\hosts



Add this line at the bottom:



127.0.0.1 PKILabServer.com



This maps the hostname to the local machine.



1️⃣4️⃣ Start the secure OpenSSL test server



Run the secure server:



"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" s\_server -cert server.pem -www -cipher "DEFAULT:@SECLEVEL=0"



Enter the password for server.pem when prompted.



When the server starts successfully, it should show:



ACCEPT

1️⃣5️⃣ Open the secure site in Firefox



Open Firefox and go to:



https://PKILabServer.com:4433/



At first, Firefox may show a certificate warning because it does not yet trust the CA.



1️⃣6️⃣ Import the CA certificate into Firefox



In Firefox:



Open Settings

Go to Privacy \& Security

Scroll to Certificates

Click View Certificates or Manage Certificates

Open the Authorities tab

Click Import

Choose ca.crt

Check Trust this CA to identify websites



This allows Firefox to trust the certificate chain signed by your CA.



1️⃣7️⃣ Test secure communication



After importing the CA certificate, revisit:



https://PKILabServer.com:4433/



The page should now load successfully. This demonstrates PKI-based trust and secure communication.



1️⃣8️⃣ Test certificate tampering



To demonstrate certificate integrity, open server.pem in Notepad and change one single character in the certificate block.



After saving the change:



Stop the server with Ctrl + C

Restart it:

"C:\\Program Files\\OpenSSL-Win64\\bin\\openssl.exe" s\_server -cert server.pem -www -cipher "DEFAULT:@SECLEVEL=0"



The certificate should fail to load or the browser should reject it. This proves that even a one-character change breaks trust.



After testing, restore the original PEM file:



copy /Y server\_backup.pem server.pem



Then restart the server again.



1️⃣9️⃣ Test hostname/address validation



To test name validation, open a hostname or address not fully covered by the certificate, such as:



https://127.0.0.1:4433/



This should trigger a browser warning because the certificate does not include the IP address as a valid SAN entry.



This demonstrates that secure communication depends not only on trust, but also on correct hostname or address matching.



📸 Screenshots Included



The following screenshots were collected during the lab:



Working directory

OpenSSL version

openssl.cnf copied

demoCA structure

CA creation

CA files created

Server key created

Server CSR created

Server certificate signed

Server files visible

CA certificate details

Server certificate details

Hosts file entry

server.pem created

OpenSSL web server running

Browser certificate warning

CA imported into browser

Secure site loaded

Tampered certificate result

Hostname/address mismatch result

✅ Findings Summary



This lab showed that PKI depends on:



a trusted Certificate Authority

a valid server certificate

correct hostname matching

intact certificate data

proper browser trust configuration



Important findings:



Before importing the CA certificate, the browser did not trust the server certificate

After importing the CA certificate, the secure page loaded successfully

Changing one character in the certificate caused the secure connection to fail

Using a hostname or address not properly matched by the certificate produced a validation warning

📄 Final Paper



The completed findings paper is located in:



paper/PKI\_Findings\_EricSledge.docx

🔗 Helpful Links

GitHub Docs

OpenSSL Documentation

Mozilla Support

👤 Author



Eric Sledge



⭐ This repository was created as part of a PKI certificate lab assignment.





Then save `README.md`, and in Command Prompt from your project folder run:



```cmd

git add .

git commit -m "Add completed PKI certificate lab"

