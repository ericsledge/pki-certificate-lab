# 🔐 PKI Certificate Lab

## 🧾 Complete Start-to-Finish Public Key Infrastructure Lab

This repository documents a complete **Public Key Infrastructure (PKI)** lab from start to finish.

The lab walks through how to:

- Create a private **Certificate Authority (CA)**
- Generate a **server private key**
- Create a **Certificate Signing Request (CSR)**
- Sign the server certificate with the CA
- Configure local hostname resolution
- Import the CA certificate into Firefox
- Test secure HTTPS communication using OpenSSL
- Validate certificate trust, tampering, and hostname mismatch behavior

> ⚠️ **Lab Note:**  
> The keys and certificates in this repository are for **learning only**.  
> Do **not** use these files for a real website, production system, or private network.

---

# 📚 Table of Contents

- [🧠 Project Overview](#-project-overview)
- [🎯 What This Lab Demonstrates](#-what-this-lab-demonstrates)
- [🛠️ Tools Used](#️-tools-used)
- [📁 Repository Structure](#-repository-structure)
- [✅ Before You Begin](#-before-you-begin)
- [🚀 Step-by-Step Lab Instructions](#-step-by-step-lab-instructions)
- [🧪 Testing and Validation](#-testing-and-validation)
- [📊 Expected Results](#-expected-results)
- [🛠️ Troubleshooting](#️-troubleshooting)
- [📸 Screenshots to Capture](#-screenshots-to-capture)
- [🧾 Findings Summary](#-findings-summary)
- [📄 Final Paper](#-final-paper)
- [🌐 GitHub Update Commands](#-github-update-commands)
- [👤 Author](#-author)

---

# 🧠 Project Overview

**PKI** stands for **Public Key Infrastructure**.

PKI is the system that allows computers, browsers, websites, and users to trust digital certificates. Certificates help prove that a server is who it claims to be and that communication with that server can be encrypted.

In this lab, a local **Certificate Authority** is created and used to sign a server certificate. Firefox is then configured to trust the local CA so the test website can load securely over HTTPS.

This lab shows how trust is built through:

- ✅ A trusted root **Certificate Authority**
- ✅ A server **private key**
- ✅ A **Certificate Signing Request**
- ✅ A signed server certificate
- ✅ **Subject Alternative Name** validation
- ✅ Browser trust settings
- ✅ Certificate integrity checks
- ✅ Hostname verification

---

# 🎯 What This Lab Demonstrates

By completing this lab, the user should be able to:

- Create a local project structure for a PKI lab
- Verify an OpenSSL installation
- Create a root CA private key and certificate
- Generate a server private key
- Create a server Certificate Signing Request
- Add Subject Alternative Names using a SAN configuration file
- Sign a server certificate with the local CA
- Build a PEM file for the OpenSSL test server
- Map a test hostname to localhost with the Windows hosts file
- Start an HTTPS server with OpenSSL
- Import a CA certificate into Firefox
- Confirm browser trust
- Test certificate tampering
- Test hostname mismatch behavior

---

# 🛠️ Tools Used

This lab uses the following tools:

- 🪟 **Windows Command Prompt**
- 🔐 **OpenSSL for Windows**
- 🦊 **Mozilla Firefox**
- 📝 **Notepad**
- 📁 **File Explorer**
- 🌐 **Git and GitHub**

---

# 📁 Repository Structure

The project should be organized like this:

```text
PKI_Lab_EricSledge/
├── README.md
├── cert_files/
│   ├── openssl.cnf
│   ├── san.cnf
│   ├── ca.crt
│   ├── ca.key
│   ├── ca.srl
│   ├── server.crt
│   ├── server.csr
│   ├── server.key
│   ├── server.pem
│   ├── server_backup.pem
│   └── demoCA/
│       ├── certs/
│       ├── crl/
│       ├── newcerts/
│       ├── index.txt
│       └── serial
├── screenshots/
└── paper/
    └── PKI_Findings_EricSledge.docx
```

## 📂 Folder Descriptions

| Folder | Purpose |
|---|---|
| `cert_files/` | Stores OpenSSL configuration files, CA files, server keys, CSRs, certificates, and PEM files. |
| `screenshots/` | Stores screenshots proving each major lab step was completed. |
| `paper/` | Stores the final findings paper for the lab. |

---

# ✅ Before You Begin

## 📌 Required Software

Install **OpenSSL for Windows** before starting the lab.

After installation, the OpenSSL executable is usually located here:

```cmd
C:\Program Files\OpenSSL-Win64\bin\openssl.exe
```

This README uses the full OpenSSL path so the commands work even if OpenSSL was not added to the system PATH.

---

## 🌐 Important Hostname Used in This Lab

The test hostname used throughout this lab is:

```text
PKILabServer.com
```

That hostname will be mapped to the local machine using the Windows hosts file.

---

# 🚀 Step-by-Step Lab Instructions

Follow each step in order.

---

## 🔹 Step 1: Create the Project Folders

Open **Command Prompt** and run:

```cmd
mkdir C:\Users\ilsle\Documents\PKI_Lab_EricSledge
mkdir C:\Users\ilsle\Documents\PKI_Lab_EricSledge\screenshots
mkdir C:\Users\ilsle\Documents\PKI_Lab_EricSledge\cert_files
mkdir C:\Users\ilsle\Documents\PKI_Lab_EricSledge\paper
```

Move into the certificate working folder:

```cmd
cd C:\Users\ilsle\Documents\PKI_Lab_EricSledge\cert_files
```

> 📌 **Important:**  
> All certificate commands should be run from this folder unless stated otherwise.

---

## 🔹 Step 2: Verify OpenSSL Works

Run:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" version
```

Expected result:

```text
OpenSSL 3.x.x ...
```

✅ If this command works, OpenSSL is installed correctly.

---

## 🔹 Step 3: Copy the OpenSSL Configuration File

OpenSSL uses a configuration file named:

```text
openssl.cnf
```

Try this command first:

```cmd
copy "C:\Program Files\Common Files\SSL\openssl.cnf" "C:\Users\ilsle\Documents\PKI_Lab_EricSledge\cert_files\openssl.cnf"
```

If that file does not exist, try this location instead:

```cmd
copy "C:\Program Files\OpenSSL-Win64\bin\openssl.cnf" "C:\Users\ilsle\Documents\PKI_Lab_EricSledge\cert_files\openssl.cnf"
```

Confirm that the file copied successfully:

```cmd
dir
```

You should see:

```text
openssl.cnf
```

---

## 🔹 Step 4: Create the CA Directory Structure

OpenSSL expects a CA database structure.

Create it with these commands:

```cmd
mkdir demoCA
mkdir demoCA\certs
mkdir demoCA\crl
mkdir demoCA\newcerts
type nul > demoCA\index.txt
echo 1000 > demoCA\serial
```

Confirm the structure:

```cmd
dir demoCA
```

Expected items:

```text
certs
crl
newcerts
index.txt
serial
```

---

## 🔹 Step 5: Create the Root Certificate Authority

Generate the root CA private key and self-signed CA certificate:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" req -new -x509 -days 3650 -keyout ca.key -out ca.crt -config openssl.cnf
```

When prompted, create and remember a password for:

```text
ca.key
```

Use these certificate values when prompted:

```text
Country Name: US
State or Province Name: Georgia
Locality Name: Atlanta
Organization Name: Student PKI Lab
Organizational Unit Name: Cybersecurity
Common Name: Eric Sledge Root CA
Email Address: leave blank
```

This creates two important files:

| File | Purpose |
|---|---|
| `ca.key` | Private key for the root CA. |
| `ca.crt` | Public CA certificate that will later be trusted by Firefox. |

Verify the files were created:

```cmd
dir ca.*
```

---

## 🔹 Step 6: Generate the Server Private Key

Create a 2048-bit encrypted RSA private key for the test server:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" genrsa -des3 -out server.key 2048
```

When prompted, create and remember a password for:

```text
server.key
```

This creates:

```text
server.key
```

The **server private key** proves the server owns the certificate.

---

## 🔹 Step 7: Create the SAN Configuration File

Modern browsers require a **Subject Alternative Name**, also called a **SAN**.

The SAN tells the browser which hostnames the certificate is valid for.

Create a file named:

```text
san.cnf
```

inside the `cert_files` folder.

You can create it with Notepad:

```cmd
notepad san.cnf
```

Paste this exact content into the file:

```ini
[req]
distinguished_name = req_distinguished_name
req_extensions = req_ext
prompt = no

[req_distinguished_name]
C = US
ST = Georgia
L = Atlanta
O = Student PKI Lab
OU = Cybersecurity
CN = PKILabServer.com

[req_ext]
subjectAltName = @alt_names

[alt_names]
DNS.1 = PKILabServer.com
DNS.2 = localhost
```

Save and close Notepad.

Confirm the file exists:

```cmd
dir san.cnf
```

---

## 🔹 Step 8: Create the Server Certificate Signing Request

Generate the CSR using the server private key and the SAN configuration file:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" req -new -key server.key -out server.csr -config san.cnf
```

Enter the `server.key` password when prompted.

This creates:

```text
server.csr
```

The **CSR** is the request that will be signed by the CA.

---

## 🔹 Step 9: Sign the Server Certificate with the CA

Use the CA certificate and CA private key to sign the server CSR:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365 -sha256 -extfile san.cnf -extensions req_ext
```

Enter the `ca.key` password when prompted.

This creates:

```text
server.crt
```

The file `server.crt` is the signed server certificate.

---

## 🔹 Step 10: Verify the CA Certificate

Run:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" x509 -in ca.crt -text -noout
```

Look for information such as:

```text
Issuer: ... CN = Eric Sledge Root CA
Subject: ... CN = Eric Sledge Root CA
```

Because this is a **self-signed root CA certificate**, the issuer and subject should both identify the root CA.

---

## 🔹 Step 11: Verify the Server Certificate

Run:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" x509 -in server.crt -text -noout
```

Look for:

```text
Issuer: ... CN = Eric Sledge Root CA
Subject: ... CN = PKILabServer.com
X509v3 Subject Alternative Name:
    DNS:PKILabServer.com, DNS:localhost
```

This confirms that the server certificate was signed by the CA and contains the required SAN entries.

---

## 🔹 Step 12: Build the PEM File for the Test Server

The OpenSSL test server can use a PEM file containing both the server private key and certificate.

Run:

```cmd
copy /Y server.key server.pem
type server.crt >> server.pem
copy /Y server.pem server_backup.pem
```

This creates:

| File | Purpose |
|---|---|
| `server.pem` | Combined server key and certificate used by the OpenSSL test server. |
| `server_backup.pem` | Backup copy used to restore the certificate after tampering tests. |

Confirm the files exist:

```cmd
dir server*.pem
```

---

## 🔹 Step 13: Update the Windows Hosts File

The hostname `PKILabServer.com` must point to the local computer.

Open **Notepad as Administrator**:

1. Click the Windows Start menu.
2. Search for `Notepad`.
3. Right-click **Notepad**.
4. Select **Run as administrator**.

Open this file:

```text
C:\Windows\System32\drivers\etc\hosts
```

Add this line at the bottom:

```text
127.0.0.1 PKILabServer.com
```

Save the file.

Test the hostname mapping:

```cmd
ping PKILabServer.com
```

Expected result:

```text
Pinging PKILabServer.com [127.0.0.1]
```

---

## 🔹 Step 14: Start the Secure OpenSSL Test Server

From the `cert_files` folder, run:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" s_server -cert server.pem -www -cipher "DEFAULT:@SECLEVEL=0"
```

Enter the `server.key` password if prompted.

Expected result:

```text
ACCEPT
```

> 📌 **Important:**  
> Leave this Command Prompt window open.  
> The HTTPS test server is now running.

---

## 🔹 Step 15: Open the Test Site in Firefox

Open Firefox and visit:

```text
https://PKILabServer.com:4433/
```

At first, Firefox should show a certificate warning because the browser does not yet trust the local CA.

✅ This is expected.

---

## 🔹 Step 16: Import the CA Certificate into Firefox

In Firefox:

1. Open **Settings**.
2. Go to **Privacy & Security**.
3. Scroll to **Certificates**.
4. Click **View Certificates** or **Manage Certificates**.
5. Open the **Authorities** tab.
6. Click **Import**.
7. Select this file:

```text
C:\Users\ilsle\Documents\PKI_Lab_EricSledge\cert_files\ca.crt
```

Check this option:

```text
Trust this CA to identify websites
```

Click **OK**.

Firefox now trusts certificates signed by this local CA.

---

## 🔹 Step 17: Test Secure Communication Again

Return to:

```text
https://PKILabServer.com:4433/
```

Expected result:

- ✅ The page loads successfully.
- ✅ The browser accepts the certificate.
- ✅ The HTTPS connection is trusted because Firefox now trusts the local CA.

---

# 🧪 Testing and Validation

This section explains the required tests for the lab.

---

## 🧪 Test 1: Certificate Trust Before CA Import

Before importing `ca.crt`, Firefox should warn that the certificate is not trusted.

This happens because the server certificate was signed by a local CA that Firefox does not know yet.

---

## 🧪 Test 2: Certificate Trust After CA Import

After importing `ca.crt` into Firefox and selecting:

```text
Trust this CA to identify websites
```

Firefox should trust the test site.

✅ This proves that browser trust depends on the trusted CA root.

---

## 🧪 Test 3: Certificate Tampering

Stop the OpenSSL server by pressing:

```cmd
Ctrl + C
```

Open `server.pem` in Notepad:

```cmd
notepad server.pem
```

Change one character inside the certificate block.

For example, change one letter or number between these lines:

```text
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```

Save the file.

Restart the server:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" s_server -cert server.pem -www -cipher "DEFAULT:@SECLEVEL=0"
```

Expected result:

- OpenSSL may fail to load the certificate, or
- Firefox may reject the certificate.

✅ This proves that certificates rely on integrity. Even a small change can break the certificate.

Restore the original PEM file:

```cmd
copy /Y server_backup.pem server.pem
```

Restart the server after restoring:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" s_server -cert server.pem -www -cipher "DEFAULT:@SECLEVEL=0"
```

---

## 🧪 Test 4: Hostname Mismatch

Open Firefox and visit:

```text
https://127.0.0.1:4433/
```

Expected result:

Firefox should show a certificate warning.

### Why This Happens

The certificate contains these DNS SAN entries:

```text
PKILabServer.com
localhost
```

The certificate does **not** contain:

```text
127.0.0.1
```

as an IP address SAN entry.

Because the address does not match the certificate, Firefox warns the user.

✅ This proves that HTTPS validation requires both **trust** and **name matching**.

---

# 📊 Expected Results

| Step | Expected Result |
|---|---|
| OpenSSL version check | OpenSSL version displays successfully. |
| CA creation | `ca.key` and `ca.crt` are created. |
| Server key generation | `server.key` is created. |
| CSR generation | `server.csr` is created. |
| Certificate signing | `server.crt` is created and signed by the CA. |
| SAN verification | Certificate shows `PKILabServer.com` and `localhost`. |
| Hosts file update | `PKILabServer.com` resolves to `127.0.0.1`. |
| Server start | OpenSSL displays `ACCEPT`. |
| Before CA import | Firefox shows a certificate warning. |
| After CA import | Firefox trusts the HTTPS site. |
| Tampering test | Modified certificate fails or is rejected. |
| Hostname mismatch test | Browser warns when visiting `https://127.0.0.1:4433/`. |

---

# 🛠️ Troubleshooting

Use this section if something does not work correctly.

---

## ❌ OpenSSL Is Not Recognized

Use the full OpenSSL path:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" version
```

If that fails, OpenSSL may not be installed in that location.

---

## ❌ Cannot Save the Hosts File

The hosts file requires administrator access.

Open Notepad by right-clicking it and selecting:

```text
Run as administrator
```

Then open:

```text
C:\Windows\System32\drivers\etc\hosts
```

---

## ❌ Firefox Still Shows a Warning After Importing the CA

Check the following:

- The imported file was `ca.crt`, not `server.crt`.
- The option **Trust this CA to identify websites** was checked.
- The URL is exactly:

```text
https://PKILabServer.com:4433/
```

- The hosts file contains:

```text
127.0.0.1 PKILabServer.com
```

- The server certificate contains the SAN entry:

```text
DNS:PKILabServer.com
```

---

## ❌ OpenSSL Server Will Not Start

Restore the backup PEM file:

```cmd
copy /Y server_backup.pem server.pem
```

Then restart:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" s_server -cert server.pem -www -cipher "DEFAULT:@SECLEVEL=0"
```

---

## ❌ Browser Says the Name Does Not Match

Check the SAN entries:

```cmd
"C:\Program Files\OpenSSL-Win64\bin\openssl.exe" x509 -in server.crt -text -noout
```

Confirm this appears:

```text
DNS:PKILabServer.com, DNS:localhost
```

If the SAN entries are missing, recreate the CSR and certificate using `san.cnf`.

---

# 📸 Screenshots to Capture

Capture screenshots for the following evidence:

- 📁 Project folder structure
- 🔐 OpenSSL version output
- 📄 `openssl.cnf` copied into `cert_files`
- 📁 `demoCA` folder structure
- 🏛️ CA certificate and key created
- 🔑 Server private key created
- 📄 Server CSR created
- 🧾 SAN configuration file
- ✅ Server certificate signed
- 🔎 Server certificate details showing SAN entries
- 🌐 Hosts file entry
- 📦 `server.pem` and `server_backup.pem` created
- 🟢 OpenSSL server running with `ACCEPT`
- ⚠️ Firefox warning before importing CA
- 🦊 Firefox CA import screen
- 🔒 Firefox loading the secure site successfully
- 🧪 Certificate tampering failure
- 🚫 Hostname mismatch warning

Store all screenshots in:

```text
screenshots/
```

---

# 🧾 Findings Summary

This lab demonstrated that **PKI-based authentication depends on a chain of trust**.

The local server certificate was not trusted at first because Firefox did not trust the local CA. After importing the CA certificate into Firefox and allowing it to identify websites, the browser trusted the server certificate.

The lab also showed that certificate trust requires more than encryption. The certificate must be:

- ✅ Intact
- ✅ Signed by a trusted CA
- ✅ Valid for the hostname being visited

When the certificate was changed, trust failed. When the site was accessed using an address that was not listed in the SAN field, the browser produced a warning.

## 🔑 Key Findings

- A **CA** acts as a trust anchor.
- A server certificate must be signed by a trusted CA.
- Modern browsers require **Subject Alternative Names**.
- Browser trust must be configured before a local CA is accepted.
- Certificate tampering breaks trust.
- Hostname mismatch causes browser validation warnings.

---

# 📄 Final Paper

The completed findings paper is located here:

```text
paper/PKI_Findings_EricSledge.docx
```

---

# 🌐 GitHub Update Commands

After replacing the README content, run these commands from the main project folder:

```cmd
cd C:\Users\ilsle\Documents\PKI_Lab_EricSledge
git status
git add README.md
git commit -m "Improve PKI lab README instructions"
git push
```

If this is the first time pushing the project to GitHub, use:

```cmd
git add .
git commit -m "Add completed PKI certificate lab"
git branch -M main
git remote add origin https://github.com/ericsledge/pki-certificate-lab.git
git push -u origin main
```

---

# 👤 Author

**Eric Sledge**

This repository was created as part of a **PKI certificate lab assignment**.
