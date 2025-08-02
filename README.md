# 🔐 KeyAuth.dev — Authentication & Licensing Platform

## What is KeyAuth.dev?

**KeyAuth.dev** is a modern software licensing and authentication platform that helps developers secure their applications using license keys, HWID locks, user tracking, and API integrations.

Originally known as [KeyAuth.shop](https://keyauth.dev), the service has transitioned to the `keyauth.dev` domain and continues to offer affordable, fast, and reliable license management solutions.

---

## Features

- License key generation and validation via socket server  
- Hardware ID (HWID) locking for device-specific licenses  
- User and license analytics dashboard  
- REST API for some integrations (not key validation)  
- Reseller and role management  
- Discord webhook support for event logging  
- Affordable pricing plans including free tiers for testing  
- Simple and intuitive developer dashboard

---

## Domain Transition

- The original domain was `https://keyauth.shop`  
- The official, current domain is `https://keyauth.dev`  
- `keyauth.shop` still redirects to `keyauth.dev`, but new users should use the `.dev` domain going forward

---

## License Key Validation (Socket Example)

KeyAuth.dev uses a **socket server** for license key validation rather than a typical HTTP API. Below is an example Python client showing how to authenticate a license key against the KeyAuth.dev socket server.

```python
import time
import wmi
from socket import AF_INET, SOCK_STREAM, socket
from colorama import Fore, init
import sys
import ssl

init(autoreset=True)

def get_hwid():
    return wmi.WMI().Win32_ComputerSystemProduct()[0].UUID

def authenticate(key: str) -> bool:
    try:
        context = ssl.create_default_context()
        s = socket(AF_INET, SOCK_STREAM)
        client = context.wrap_socket(s, server_hostname="socket.keyauth.shop")
        client.connect(("socket.keyauth.dev", 3389))

        client.send("2".encode())
        time.sleep(0.2)

        data = f"your_paid_key|{key}|{get_hwid()}"
        client.send(data.encode("utf-8"))

        response = client.recv(1024).decode()

        if response.startswith("TOKEN|"):
            token = response.split("|", 1)[1]
            client.send(token.encode("utf-8"))

            result = client.recv(1024).decode()
            if result.startswith("ACCESS:"):
                import hashlib
                secret = "S3rv3rS3cr3t"
                expected = hashlib.sha256((token + secret).encode()).hexdigest()
                return result == f"ACCESS:{expected}"
            else:
                print(Fore.RED + f"[KeyAuth] Server refused: {result}")
                return False
        
        elif response.startswith("ACCESS:"):
            print(Fore.GREEN + "[KeyAuth] Authentication successful!")
            return True
        elif response == "Banned":
            print(Fore.RED + "[KeyAuth] This key or HWID is banned.")
            return False
        elif response == "HWID Mismatch":
            print(Fore.RED + "[KeyAuth] HWID does not match the registered device.")
            return False
        elif response == "No":
            print(Fore.RED + "[KeyAuth] Invalid key.")
            return False
        else:
            print(Fore.RED + f"[KeyAuth] Unexpected response: {response}")
            return False

    except Exception as e:
        print(f"[KeyAuth] Connection error: {e}")
        return False

# Usage example:
key = input("Enter your license key: ")
if authenticate(key):
    print(Fore.GREEN + "[KeyAuth] Authentication successful!")
    # Your program code here
else:
    sys.exit(1)

