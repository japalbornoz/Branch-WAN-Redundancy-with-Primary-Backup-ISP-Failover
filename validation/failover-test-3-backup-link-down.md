# Failover Test 3 – Backup WAN Link Down While Primary Remains Active

## Objective
Validate that failure of the **backup WAN link** does not impact production traffic while the **primary WAN link** remains healthy.

## Failure Scenario
The secondary serial link between **R1** and **ISP2** is administratively shut down while **ISP1 remains operational**.

## Change Performed
On R1:
```cisco
configure terminal
interface s0/0/1
shutdown
end
```
This simulates a failure of the standby WAN path.

## Expected Behavior
- the primary default route via ISP1 remains active
- production traffic continues to use ISP1
- PC1 and SRV1 continue to reach the external server
- the backup path becomes unavailable, but no failover occurs

## Validation Steps
### 1. Verify interface state
Run:
```cisco
show ip interface brief
```
Expected:
- `Serial0/0/1` = administratively down/down
- `Serial0/0/0` = up/up
This confirms that the backup WAN link is down while the primary link remains active.

### 2. Verify routing table
Run:
```cisco
show ip route
```
Expected active default route:
```text
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```
This confirms that ISP1 continues to serve as the active outbound path.

### 3. Verify external reachability from R1
Run:
```cisco
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute continues to show the path through ISP1

### 4. Verify end-host connectivity from PC1
From PC1 command prompt:
```text
ping 8.8.8.8
```
Expected:
- connectivity remains available

### 5. Verify end-host connectivity from SRV1
From SRV1 command prompt:
```text
ping 8.8.8.8
```
Expected:
- connectivity remains available

## Result
The backup WAN link was successfully taken out of service while the primary WAN path remained operational. R1 continued using ISP1 as the active default route, and branch connectivity to the external server remained unaffected.

## Evidence
- [failover3-r1-show-ip-interface-brief.png](screenshots/failover3-r1-show-ip-interface-brief.png)
- [failover3-r1-show-ip-route.png](screenshots/failover3-r1-show-ip-route.png)
- [failover3-r1-ping-8.8.8.8.png](screenshots/failover3-r1-ping-and-traceroute-8.8.8.8.png)
- [failover3-pc1-ping-8.8.8.8.png](screenshots/failover3-pc1-ping-8.8.8.8.png)
- [failover3-srv1-ping-8.8.8.8.png](screenshots/failover3-srv1-ping-8.8.8.8.png)
