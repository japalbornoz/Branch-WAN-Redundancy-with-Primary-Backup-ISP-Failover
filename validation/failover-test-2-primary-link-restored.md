# Failover Test 2 – Primary WAN Link Restored

## Objective
Validate that the branch router returns to the **primary ISP path** after the primary WAN link is restored.

## Recovery Scenario
The R1-to-ISP1 serial link is brought back online.

## Change Performed
On R1:
```cisco
configure terminal
interface s0/0/0
no shutdown
end
```

Expected Behavior
- R1 restores the primary connected path
- the lower administrative distance default route via ISP1 becomes active again
- backup route via ISP2 remains configured but inactive
- external traffic returns to the primary WAN path


## Validation Steps
### 1. Verify interface state
Run:
```
show ip interface brief
```
Expected:
- Serial0/0/0 = up/up
- Serial0/0/1 = up/up

### 2. Verify routing table change
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
- traceroute uses ISP1 path again

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
After restoring the primary WAN link, R1 reinstalled the primary default route through ISP1 and resumed normal outbound routing through the preferred WAN provider.

## Evidence to Capture
- show ip interface brief on R1 after restoration
- show ip route on R1 showing ISP1 as active default route
- ping 8.8.8.8 from R1
- traceroute 8.8.8.8 from R1
- ping 8.8.8.8 from PC1
- ping 8.8.8.8 from SRV1
