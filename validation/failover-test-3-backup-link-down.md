# Failover Test 3 – Backup WAN Link Down While Primary Remains Active

## Objective
Validate that failure of the **backup WAN link** does not impact production traffic while the **primary WAN link** remains healthy.

## Failure Scenario
The secondary serial link between R1 and ISP2 is administratively shut down while ISP1 stays available.

## Change Performed
On R1:
```cisco
configure terminal
interface s0/0/1
shutdown
end
```

## Expected Behavior
- primary default route via ISP1 remains active
- user traffic is not impacted
- PC1 and SRV1 continue to reach the external server
- backup path becomes unavailable, but no failover is needed

## Validation Steps
### 1. Verify interface state
Run:
```
show ip interface brief
```
Expected:
- Serial0/0/1 = administratively down/down
- Serial0/0/0 = up/up

### 2. Verify routing table
Run:
```
show ip route
```
Expected active default route:
```
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```

### 3. Verify external reachability from R1
Run:
```
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute still uses ISP1

### 4. Verify end-host connectivity from PC1
Run:
```
ping 8.8.8.8
```
Expected:
- connectivity remains available

### 5. Verify end-host connectivity from SRV1
Run:
```
ping 8.8.8.8
```
Expected:
- connectivity remains available

## Result
The backup WAN path was successfully taken out of service while the primary path remained available. R1 continued using ISP1 as the active default route, and branch connectivity to the external server was unaffected.

## Evidence to Capture
- Save screenshots for:
- show ip interface brief on R1 after backup link shutdown
- show ip route on R1 showing ISP1 still active
- ping 8.8.8.8 from R1
- traceroute 8.8.8.8 from R1
- ping 8.8.8.8 from PC1
- ping 8.8.8.8 from SRV1
