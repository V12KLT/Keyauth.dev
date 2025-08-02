# 🔐 KeyAuth.dev — Authentication & Licensing Platform

## What is KeyAuth.dev?

**KeyAuth.dev** is a modern software licensing and authentication platform that helps developers secure their applications using license keys, HWID locks, user tracking, and API integrations.

Originally known as [KeyAuth.shop](https://keyauth.dev), the service has transitioned to the `keyauth.dev` domain and continues to offer affordable, fast, and reliable license management solutions.

---

## Features

- License key generation and validation  
- Hardware ID (HWID) locking for device-specific licenses  
- User and license analytics dashboard  
- REST API for seamless integration  
- Reseller and role management  
- Discord webhook support for event logging  
- Affordable pricing plans including free tiers for testing  
- Simple and intuitive developer dashboard

---

## Domain Transition

- The original domain was `https://keyauth.dev`  
- The official, current domain is `https://keyauth.shop`  
- `keyauth.shop` still redirects to `keyauth.dev`, but new users should use the `.dev` domain going forward

---

## API Example

Example request to validate a license key:

```http
POST https://keyauth.dev/api/v1/validate
Content-Type: application/json

{
  "key": "ABC-123-XYZ",
  "hwid": "DEVICE-HWID-12345",
  "program": "YOUR_PROGRAM_ID"
}
