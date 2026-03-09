# Issues Encountered

## 1. Return-path routing problem during WAN failover

### Symptom
During the primary WAN failure test, R1 successfully failed over to ISP2 and could reach the external server. However, **PC1 and SRV1 could not complete ping or traceroute to 8.8.8.8**.

Using Packet Tracer simulation mode showed that:
- outbound traffic from the branch correctly followed the **ISP2 path**
- return traffic from the INET router was still forwarded toward **ISP1**

Because the R1–ISP1 serial link was down, the return packets were dropped.

### Cause
The INET router still preferred ISP1 for the branch LAN return routes:
```text
ip route 192.168.10.0 255.255.255.0 100.64.1.1
ip route 192.168.20.0 255.255.255.0 100.64.1.1
```
INET was unaware that the R1–ISP1 link had failed because its own interface toward ISP1 remained operational. This caused **asymmetric routing**, where the forward and return paths used different ISPs.

### Fix
Adjusted static routes on INET so that return traffic preference aligned with the intended WAN design.

Final configuration:
```text
ip route 192.168.10.0 255.255.255.0 100.64.1.1
ip route 192.168.20.0 255.255.255.0 100.64.1.1
ip route 192.168.10.0 255.255.255.0 100.64.2.1 10
ip route 192.168.20.0 255.255.255.0 100.64.2.1 10
```
This ensured that return traffic followed the **primary ISP1 path under normal conditions**, while ISP2 remained available as a standby path.

---

## 2. Missing return routes for branch WAN subnets

### Symptom
R1 initially failed to ping some upstream transit interfaces and the external server even though PC1 could reach the destination.

### Cause
The INET router did not initially have routes for the branch WAN /30 networks:

- `203.0.113.0/30`
- `198.51.100.0/30`

Without these routes, return packets to R1's WAN interfaces could not be forwarded correctly.

### Fix
Added static routes on INET for the WAN transit subnets:
```text
ip route 203.0.113.0 255.255.255.252 100.64.1.1
ip route 198.51.100.0 255.255.255.252 100.64.2.1
```

---

## 3. Serial interface clocking (DCE/DTE)

### Symptom
Serial WAN links initially failed to come up during topology deployment.

### Cause
Clocking must be provided by the **DCE side of a serial connection**.

### Fix
Configured the ISP routers as the DCE side and applied clocking:
clock rate 64000

This allowed the serial links to establish properly.

---

## 4. Router-originated traffic behavior during failover

### Symptom
Ping results from R1 differed depending on which WAN path was active.

### Cause
Router-originated traffic follows the **currently active routing table entry**, typically the default route unless a more specific route exists.

During failover, R1 switched its default route to ISP2, which changed the path used by router-generated traffic.

### Fix
Validation focused on:
- verifying the active default route
- checking traceroute paths
- confirming end-to-end host connectivity

rather than relying solely on individual interface ping tests.
