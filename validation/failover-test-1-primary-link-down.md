# Failover Test 1 – Primary WAN Link Down

## Objective
Validate that traffic fails over from **ISP1** to **ISP2** when the primary WAN link becomes unavailable.

## Failure Scenario
The primary serial link between **R1** and **ISP1** is administratively shut down to simulate a WAN outage.

## Change Performed
On R1:

```cisco
configure terminal
interface s0/0/0
shutdown
end
```
This simulates loss of the primary WAN circuit.


## Expected Behavior
- R1 removes the primary default route from the routing table
- the floating static route via **ISP2** becomes active
- PC1 still reaches the external server
- SRV1 still reaches the external server
- traceroute now follows the **ISP2 path**

## Validation Steps
### 1. Verify interface state
Run on R1:
```cisco
show ip interface brief
```
Expected:
- Serial0/0/0 = administratively down/down
- Serial0/0/1 = up/up
This confirms the primary WAN link is down.

### 2. Verify routing table change
Run:
```cisco
show ip route
```

Expected active default route:

```cisco
S* 0.0.0.0/0 [10/0] via 198.51.100.1
```
This confirms that **ISP2 is now the active outbound path**.

### 3. Verify external reachability from R1
Run:
```cisco
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute follows ISP2 instead of ISP1

### 4. Verify end-host connectivity from PC1
```From PC1 command prompt:
ping 8.8.8.8
```
Expected:
- connectivity remains available
- a brief packet loss may occur during route transition


### 5. Verify end-host connectivity from SRV1
```From SRV1 command prompt:
ping 8.8.8.8
```
Expected:
- connectivity remains available
- brief packet loss may occur during route convergence

## Result
The primary WAN failure scenario was successfully simulated. R1 removed the primary default route and activated the floating backup route through ISP2. End-to-end connectivity to the external server remained available through the backup WAN path.

## Evidence
- [R1 interface state after shutdown](screenshots/failover1-r1-show-ip-interface-brief.png)
- [R1 routing table showing backup default route](screenshots/failover1-r1-show-ip-route.png)
- [R1 ping 8.8.8.8 via ISP2](screenshots/failover1-r1-ping-8.8.8.8.png)
- [R1 traceroute via ISP2](screenshots/failover1-r1-traceroute-8.8.8.8.png)
- [PC1 ping during failover](screenshots/failover1-pc1-ping-8.8.8.8.png)
- [SRV1 ping during failover](screenshots/failover1-srv1-ping-8.8.8.8.png)
