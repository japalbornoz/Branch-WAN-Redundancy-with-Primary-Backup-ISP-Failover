# Failover Test 2 – Primary WAN Link Restored

## Objective
Validate that the branch router returns to the **primary ISP path** after the primary WAN link is restored.

## Recovery Scenario
The **R1–ISP1 serial link** is brought back online after the simulated WAN failure.

## Change Performed
On R1:
```cisco
configure terminal
interface s0/0/0
no shutdown
end
```
This restores the primary WAN circuit.

## Expected Behavior
- R1 restores the primary connected path
- the lower administrative distance default route via **ISP1** becomes active again
- the floating static route via **ISP2** remains configured but inactive
- outbound traffic returns to the primary WAN path

## Validation Steps
### 1. Verify interface state from R1
Run:
```cisco
show ip interface brief
```
Expected:
- `Serial0/0/0` = up/up
- `Serial0/0/1` = up/up
This confirms that the primary WAN link is operational again.



### 2. Verify routing table change from R1 
Run:
```cisco
show ip route
```
Expected active default route:
```cisco
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```
This confirms that ISP1 has resumed its role as the preferred outbound path.



### 3. Verify external reachability from R1
Run:
```cisco
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute shows the path through ISP1 again



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
After restoring the primary WAN link, R1 reinstalled the primary default route through ISP1 and resumed normal outbound routing through the preferred WAN provider. End-to-end connectivity remained operational throughout the restoration process.

## Evidence 
- [R1 interface state after restoration](screenshots/failover2-r1-show-ip-interface-brief.png)
- [R1 routing table showing ISP1 active](screenshots/failover2-r1-show-ip-route.png)
- [R1 ping 8.8.8.8](screenshots/failover2-r1-ping-8.8.8.8.png)
- [R1 traceroute via ISP1](screenshots/failover2-r1-traceroute-8.8.8.8.png)
- [PC1 ping after restoration](screenshots/failover2-pc1-ping-8.8.8.8.png)
- [SRV1 ping after restoration](screenshots/failover2-srv1-ping-8.8.8.8.png)

- show ip interface brief on R1 after restoration
- show ip route on R1 showing ISP1 as active default route
- ping 8.8.8.8 from R1
- traceroute 8.8.8.8 from R1
- ping 8.8.8.8 from PC1
- ping 8.8.8.8 from SRV1
