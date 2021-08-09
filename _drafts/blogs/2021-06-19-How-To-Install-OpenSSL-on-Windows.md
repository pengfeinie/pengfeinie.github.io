---
title: 'How To Install OpenSSL on Windows'
date: 2021-06-19
tags:
  - Windows
  - OpenSSL
---

OpenSSL is a full-featured toolkit for the Transport Layer Security (TLS) and Secure Sockets Layer (SSL) protocols. It is licensed under an Apache-style license. This tutorial will help you to install OpenSSL on Windows operating systems.

## Step 1 – Download OpenSSL Binary

Download the latest OpenSSL windows installer file from the following download page. Click the below link to visit OpenSSL download page:

[http://slproweb.com/products/Win32OpenSSL.html](https://slproweb.com/products/Win32OpenSSL.html)

[![img](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-download.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-download.png)

## Step 2 – Run OpenSSL Installer

Now run the OpenSSL installer on your system. The OpenSSL required Microsoft Visual C++ to be installed on your system. If your system doesn’t have Microsoft Visual C++ installed, the installer will show your message like:

[![img](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-install-1.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-install-1.png)

Click **Yes** to download and install required Microsoft Visual C++ package on your system.

[![img](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-requirements.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-requirements.png)

Then again run the OpenSSL installer and follow the wizard.

[![img](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-install-2.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-install-2.png)

## Step 3 – Setup Environment Variables

Now set the environment variables to function OpenSSL properly on your system. You are required to set OPENSSL_CONF and Path environment variables.

Use the following commands to set environment for current session only:

```
set OPENSSL_CONF=C:\OpenSSL-Win32\bin\openssl.cfg
set Path=%Path%;C:\OpenSSL-Win32\bin
```

**Set Variables Permanently** – You can also se the OPENSSL_CONF and Path environment variables in system permanently. To set the environment variable follow:

Press Windows + R keys together to open run window, Then type “**sysdm.cpl**” in the Run dialog box and hit Enter. Alternatively, you can open Command Prompt and type the same command to open System Properties

Go to “Advanced” tab and click on “Environment variables”.

**Set OPENSSL_CONF Variable:**
[![img](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-environment1.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-environment1.png)

**Set Path Variable:**
[![img](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-environment2.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-environment2.png)

## Step 4 – Run OpenSSL Binary

Open a command prompt on your system and type **openssl** to open OpenSSL prompt. After that type **`version`** to get the installed OpenSSL version on your system.

[![check openssl version windows](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-version.png)](https://tecadmin.net/wp-content/uploads/2018/12/openssl-windows-version.png)



[https://tecadmin.net/install-openssl-on-windows/](https://tecadmin.net/install-openssl-on-windows/)

