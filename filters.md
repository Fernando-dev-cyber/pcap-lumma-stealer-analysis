## 🛠️ Wireshark Filters Used

```wireshark```

- **C2 Traffic Isolation:**
  ip.addr == 153.92.1.49

- **Host Discovery (NetBIOS & DHCP):**
    nbns or dhcp


- **User & Identity Extraction (SAMR / Kerberos):**
    samr.user_name or samr.full_name or kerberos.CNameString


- **Domain Verification:**
    http.host and ip.addr == 153.92.1.49
