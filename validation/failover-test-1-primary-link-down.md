# Failover Test 1 – Primary WAN Link Down

## Objective
Validate that traffic fails over from **ISP1** to **ISP2** when the primary WAN link is unavailable.

## Failure Scenario
The primary serial link between R1 and ISP1 is administratively shut down.

## Change Performed
On R1:
```cisco
configure terminal
interface s0/0/0
shutdown
end
```
## Expected Behavior
- R1 removes the primary default route from the routing table
- the floating static route via ISP2 becomes active
- PC1 still reaches the external server
- SRV1 still reaches the external server
- traceroute now follows the ISP2 path

## Validation Steps
### 1. Verify interface state
Run on R1:
```
show ip interface brief
```
Expected:
- Serial0/0/0 = administratively down/down
- Serial0/0/1 = up/up

### 2. Verify routing table change
Run:
```
show ip route
```

Expected active default route:
```
S* 0.0.0.0/0 [10/0] via 198.51.100.1
```
This confirms that ISP2 is now the active path.


### 3. Verify external reachability from R1
Run:
```
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute follows ISP2 instead of ISP1

### 4. Verify end-host connectivity from PC1
Run:
```
ping 8.8.8.8
```
Expected:
- connectivity remains available
- there may be brief packet loss during failover


### 5. Verify end-host connectivity from SRV1
Run:
```
ping 8.8.8.8
```
Expected:
- connectivity remains available
- brief packet loss may occur during route transition

## Result
The primary WAN failure was successfully simulated. R1 removed the primary default route and activated the floating backup route through ISP2. End-to-end connectivity to the external server remained available through the backup WAN path.

## Evidence to Capture
- show ip interface brief on R1 after shutdown
- show ip route on R1 showing backup default route
- ping 8.8.8.8 from R1
- traceroute 8.8.8.8 from R1
- ping 8.8.8.8 from PC1 during failover
- ping 8.8.8.8 from SRV1 during failover
