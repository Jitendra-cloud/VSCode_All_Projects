# SAP A4H Fiori + Eclipse Connectivity Troubleshooting

## 1. Original problems

Two issues were investigated:

1. `/N/UI2/FLP` / Fiori Launchpad showed **Server not found**.
2. Eclipse/ADT could not create an ABAP Project and showed:
   `Logon to system S4H failed: hostname 's1.login2server.com' unknown`.

The purpose was to determine whether the problem was SAP configuration, Fiori/SICF, ICM, DNS, network, VPN, or Eclipse.

---

## 2. The three connection paths

### SAP GUI

```text
SAP GUI
  -> s1.login2server.com
  -> Instance 62
  -> Dispatcher port 3262
```

### Eclipse / ADT

```text
Eclipse/ADT
  -> s1.login2server.com
  -> Instance 62
  -> Dispatcher port 3262
```

### Fiori

```text
Browser
  -> s4h2023.sapdemo.com
  -> HTTPS 44303
  -> /sap/bc/ui2/flp
```

The important point is that SAP GUI, Eclipse and Fiori can use different hostnames and ports.

---

## 3. SICF check

Transaction:

```text
SICF
```

Service checked:

```text
/default_host/sap/bc/ui2/flp
```

Result:

```text
Service (Active)
```

### Why

SICF controls HTTP services in SAP. This check verifies that the Fiori Launchpad service is activated.

### Conclusion

`/sap/bc/ui2/flp` was already active. No activation change was needed.

---

## 4. SMICM check

Transaction:

```text
SMICM
-> Goto -> Services
```

The services included:

```text
HTTP   8003   Active
HTTPS  44303  Active
HTTPS  5203   Active
```

### Why

SMICM manages HTTP/HTTPS communication. Fiori needs the HTTPS listener.

### Conclusion

HTTPS port `44303` was active.

---

## 5. SM51 check

Transaction:

```text
SM51
```

The SAP instance was:

```text
s4h2023_A4H_03
Host: s4h2023
State: Active
```

### Why

SM51 verifies that the SAP application server instance is running.

### Conclusion

The SAP application server was active.

---

## 6. Internal SAP server IP

### How did we get `192.168.1.161`?

We did **not guess this IP**. We obtained it from the SAP system itself using SAP GUI.

The steps were:

1. Log in to the SAP system using **SAP GUI**.
2. Open the SAP system information / status screen.
3. Look at the **SAP server / host information** for the application server instance.
4. The SAP server host was shown as:

```text
s4h2023
```

5. The server information also showed its internal IP address:

```text
s4h2023 → 192.168.1.161
```

So `192.168.1.161` was the **internal network IP of the SAP application server**.

### Why did we check this IP?

At that point, we wanted to determine whether the user's PC could directly reach the SAP server over the internal network.

The PC's network information showed:

```text
PC IP:       10.32.129.93
Subnet:      255.255.255.0
Gateway:     10.32.129.59
```

The SAP server was:

```text
SAP server:  s4h2023
Internal IP: 192.168.1.161
```

We then tested connectivity from Windows:

```powershell
ping 192.168.1.161
```

and later:

```powershell
Test-NetConnection 192.168.1.161 -Port 44303
```

Those tests did not establish connectivity to the internal IP.

### Important distinction

Later, we discovered that the public/application-server hostname:

```text
s1.login2server.com
```

resolved to:

```text
124.123.22.43
```

and that:

```text
Test-NetConnection 124.123.22.43 -Port 44303
```

returned:

```text
TcpTestSucceeded : True
```

Therefore, `192.168.1.161` and `124.123.22.43` should **not be treated as the same endpoint**.

```text
SAP internal server
s4h2023
192.168.1.161
       │
       │ internal/private network
       ↓
   SAP system

External/application-server endpoint
s1.login2server.com
124.123.22.43
       │
       │ reachable from the PC
       ↓
    PC: 10.32.129.93
```

### General lesson

When you need an SAP server's IP address, **get it from SAP's server/system information rather than guessing it from the PC's network settings**.

The PC's `ipconfig` tells us the **PC's** IP. SAP's server information tells us the **SAP server's** host/IP.


## 7. Windows network information

`ipconfig` showed:

```text
IPv4:       10.32.129.93
Subnet:     255.255.255.0
Gateway:    10.32.129.59
DNS server: 10.32.129.59
```

This is different from the internal SAP IP `192.168.1.161`.

---

## 8. DNS test for Fiori hostname

Command:

```cmd
nslookup s4h2023.sapdemo.com
```

Initial result:

```text
Non-existent domain
```

### Why

`nslookup` checks DNS. The configured DNS server did not have a record for `s4h2023.sapdemo.com`.

This explained the original browser hostname-resolution problem.

---

## 9. Test of internal IP

Command:

```cmd
ping 192.168.1.161
```

Result:

```text
Request timed out
100% loss
```

`tracert 192.168.1.161` also did not reach the destination.

### Important

Ping uses ICMP. A ping failure does not necessarily mean an application port is unavailable.

For application connectivity, TCP port testing is more useful.

---

## 10. SAP GUI connection details

SAP GUI Properties showed:

```text
System ID:          S4H
Application Server: s1.login2server.com
Instance Number:    62
```

### Important correction

The `03` in `s4h2023_A4H_03` is part of the SAP instance name shown in SM51. The SAP GUI connection's actual instance number is `62`.

For standard SAP Dispatcher communication:

```text
32 + 62 = 3262
```

So the SAP Dispatcher port is:

```text
3262
```

---

## 11. DNS test for SAP GUI hostname

Command:

```cmd
nslookup s1.login2server.com
```

Result:

```text
Name:    s1.login2server.com
Address: 124.123.22.43
```

Therefore:

```text
s1.login2server.com -> 124.123.22.43
```

---

## 12. SAP Dispatcher connectivity

Command:

```powershell
Test-NetConnection s1.login2server.com -Port 3262
```

Result:

```text
RemoteAddress      : 124.123.22.43
RemotePort         : 3262
TcpTestSucceeded   : True
```

### Conclusion

The PC can reach the SAP application server through:

```text
s1.login2server.com:3262
```

This explains why SAP GUI can connect.

---

## 13. Eclipse / ADT issue

Eclipse initially reported:

```text
Logon to system S4H failed:
hostname 's1.login2server.com' unknown
```

The Eclipse connection was configured with:

```text
System ID:          S4H
Connection Type:    Custom Application Server
Application Server: s1.login2server.com
Instance Number:    62
```

Changing the Application Server temporarily to:

```text
124.123.22.43
```

made the Eclipse connection work.

### Conclusion

The network and SAP Dispatcher were reachable. Using the IP bypassed the hostname-resolution problem affecting Eclipse/Java.

Working diagnostic configuration:

```text
System ID:          S4H
Application Server: 124.123.22.43
Instance Number:    62
Client:             100
Language:           EN
```

---

## 14. Windows hosts file

The hosts file is:

```text
C:\Windows\System32\drivers\etc\hosts
```

It already contained several internal hostname mappings.

There was no entry for:

```text
s4h2023.sapdemo.com
```

### Purpose

A hosts-file entry can provide local hostname-to-IP resolution when DNS does not provide the required record.

The relevant mapping used for the Fiori hostname was:

```text
124.123.22.43    s4h2023.sapdemo.com    s4h2023
```

Do not randomly add IP addresses. The IP should be verified first.

---

## 15. Fiori HTTPS connectivity test

First, the hostname itself was not available through DNS:

```cmd
nslookup s4h2023.sapdemo.com
```

Then the IP/port was tested:

```powershell
Test-NetConnection 124.123.22.43 -Port 44303
```

Result:

```text
RemoteAddress      : 124.123.22.43
RemotePort         : 44303
TcpTestSucceeded   : True
```

This proved that the PC can reach HTTPS port `44303` on `124.123.22.43`.

---

## 16. Verify the hosts-file mapping

After the hosts mapping, normal Windows hostname resolution showed:

```cmd
ping s4h2023.sapdemo.com
```

as:

```text
Pinging s4h2023.sapdemo.com [124.123.22.43]
```

The ping itself timed out. That is not a problem by itself because ICMP can be blocked.

The important test was:

```powershell
Test-NetConnection s4h2023.sapdemo.com -Port 44303
```

Result:

```text
RemoteAddress      : 124.123.22.43
RemotePort         : 44303
TcpTestSucceeded   : True
```

### Important point about `nslookup`

Even after a hosts-file entry exists, `nslookup` can still say:

```text
Non-existent domain
```

because `nslookup` queries the DNS server directly.

Normal Windows applications can still resolve the hostname through the hosts file.

---

## 17. VPN conclusion

A VPN was considered because the SAP internal IP was:

```text
192.168.1.161
```

while the PC was:

```text
10.32.129.93
```

However, the working public/application-server tests showed:

```text
s1.login2server.com:3262 -> True
124.123.22.43:44303       -> True
```

Therefore, there was no evidence that a VPN was required for these tested endpoints.

If the provider specifically requires a VPN, use only the VPN configuration supplied by the provider. Do not guess a VPN server address or Cloud ID.

---

## 18. Final connection picture

```text
                    SAP S4H / A4H
                         |
             +-----------+-----------+
             |                       |
          SAP GUI                 Fiori
             |                       |
 s1.login2server.com       s4h2023.sapdemo.com
             |                       |
       124.123.22.43           124.123.22.43
             |                       |
         Port 3262               Port 44303
             |                       |
             +-----------+-----------+
                         |
                    SAP system
```

### Eclipse / ADT

```text
Eclipse
  -> 124.123.22.43
  -> Port 3262
  -> S4H
  -> Working
```

### Fiori

```text
Browser
  -> s4h2023.sapdemo.com
  -> 124.123.22.43
  -> Port 44303
  -> SAP ICM
  -> /sap/bc/ui2/flp
```

The TCP connectivity to the Fiori endpoint was verified successfully.

---

## 19. Transactions used

| Transaction | Purpose |
|---|---|
| `SICF` | Check Fiori HTTP service |
| `SMICM` | Check HTTP/HTTPS listeners |
| `SM51` | Check SAP application server |
| SAP GUI Properties | Find application server and instance number |
| `/N/UI2/FLP` | Launch Fiori Launchpad |
| `SE78` | SAP graphics/logo management; unrelated to this connectivity issue |

---

## 20. Windows commands used

| Command | Purpose |
|---|---|
| `ipconfig` | Show PC IP, gateway and DNS |
| `ping` | ICMP reachability test |
| `tracert` | Trace network path |
| `nslookup` | Test DNS resolution |
| `Test-NetConnection` | Test TCP connectivity to a host/port |
| `ipconfig /flushdns` | Clear Windows DNS cache |

For SAP application troubleshooting, `Test-NetConnection` is generally more useful than ping because SAP services use TCP ports.

---

## 21. Current status

### SAP GUI

```text
Server:   s1.login2server.com
IP:       124.123.22.43
Instance: 62
Port:     3262
Status:   Working
```

### Eclipse / ADT

```text
Server:   124.123.22.43
Instance: 62
Status:   Working
```

### Fiori network connectivity

```text
Host:     s4h2023.sapdemo.com
IP:       124.123.22.43
Port:     44303
Status:   TCP reachable
```

### Fiori service

```text
/sap/bc/ui2/flp
Status: Active
```

---

## 22. Main lessons

1. SAP GUI working does not mean Fiori and Eclipse use the same hostname/port.
2. `SICF` confirms whether an HTTP service is active.
3. `SMICM` confirms whether SAP is listening on HTTP/HTTPS ports.
4. `SM51` confirms the SAP instance is running.
5. DNS resolution and TCP connectivity are different problems.
6. Ping can fail even when an application port works.
7. `Test-NetConnection` is useful for testing a specific SAP port.
8. Instance `62` corresponds to standard Dispatcher port `3262`.
9. A hosts-file entry can fix local hostname resolution.
10. A hosts-file entry does not fix routing or firewall problems.
11. Do not guess VPN details or server IPs.
12. Verify the actual endpoint and port before changing configuration.


## Why SICF: `sap → bc → ui2 → flp`?

This was **not a random path selection**. The path came directly from the Fiori Launchpad URL we were troubleshooting.

Example URL:

```text
https://s4h2023.sapdemo.com:44303/sap/bc/ui2/flp?sap-client=100
```

The important part is:

```text
/sap/bc/ui2/flp
```

SAP's **SICF (Internet Communication Framework)** organizes HTTP services in a tree that follows this path structure. Therefore, the URL path can be mapped directly to the SICF tree:

```text
Fiori URL path                 SICF tree
────────────────────────────────────────────
/sap                           → sap
   /bc                         → bc
      /ui2                     → ui2
         /flp                  → flp
```

So we navigated in SICF:

```text
default_host
   └── sap
       └── bc
           └── ui2
               └── flp
```

### How to identify the SICF service yourself

When troubleshooting an SAP HTTP/Fiori URL:

1. **Look at the URL.**
2. Ignore the protocol, hostname, and port for this particular check.
3. Take the URL path beginning with `/sap/...`.
4. Compare that path with the SICF service tree.
5. Open the matching service and check whether it is active.

For example:

| URL path | What to investigate |
|---|---|
| `/sap/bc/ui2/flp` | Fiori Launchpad SICF service |
| `/sap/opu/odata/...` | OData HTTP services |
| `/sap/public/...` | Public SAP web services/resources |

### Why not just check `sap`?

Because `sap` is only a **higher-level folder** containing many different HTTP services.

We need to check the **specific endpoint** requested by the browser. In our case, the browser was requesting:

```text
/sap/bc/ui2/flp
```

Therefore, the relevant SICF service was:

```text
/sap/bc/ui2/flp
```

### Important: "Service Active" does not mean Fiori is fully working

When SICF showed:

```text
Service (Active)
```

we proved only that the corresponding SAP HTTP service was activated.

It did **not** prove that the complete Fiori connection was working.

A useful troubleshooting model is:

```text
Browser
   ↓
DNS / hostname resolution
   ↓
Network connectivity
   ↓
TCP port 44303
   ↓
HTTPS / SAP ICM
   ↓
SICF service: /sap/bc/ui2/flp
   ↓
Fiori Launchpad application/configuration
   ↓
Authentication / authorization
```

This distinction helped us separate the problems:

- `s1.login2server.com` / port `3262` → SAP GUI / Eclipse ABAP connection
- `s4h2023.sapdemo.com` / port `44303` → Fiori HTTPS connection
- `/sap/bc/ui2/flp` → the specific Fiori Launchpad HTTP service inside SAP

### General troubleshooting principle

**Don't guess the SAP transaction or service to check. Start from the symptom and trace it backward.**

For a browser URL, the URL gives you valuable clues:

```text
Hostname        → DNS / hosts file
Port            → network / firewall / ICM service
URL path        → SICF service
SAP client      → SAP logon client
Application     → Fiori/ICF/application configuration
```

This is why the SICF check specifically went to:

```text
default_host → sap → bc → ui2 → flp
```
