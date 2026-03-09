# Baseline Validation

## Objective
Confirm that the branch WAN redundancy topology is operating normally with the **primary ISP path active** before failure testing begins.

## Expected State
- R1 prefers **ISP1** as the active default route
- PC1 can reach the external server
- SRV1 can reach the external server
- R1 can reach upstream transit interfaces and the external server
- traceroute is expected to prefer the primary WAN path through ISP1

## Validation Scope
The following devices and paths were included in baseline validation:
- **R1** as the branch edge router
- **ISP1** as the primary WAN path
- **ISP2** as the standby WAN path
- **INET** as the simulated upstream internet router
- **PC1** as the user endpoint
- **SRV1** as the internal server endpoint
- **EXT-SRV** as the external reachability target

## Validation Steps

### 1. Verify interface status on R1
Run:
```cisco
show ip interface brief
```
Expected:
`GigabitEthernet0/0` is up/up
`GigabitEthernet0/1` is up/up
`Serial0/0/0` is up/up
`Serial0/0/1` is up/u

### 2. Verify R1 routing table  
Run:
```cisco
show ip route
```
Expected active default route:
```
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```
This confirms that ISP1 is the preferred outbound path under normal conditions.

### 3. Verify WAN and upstream reachability from R1
Run:
```cisco
ping 203.0.113.1
ping 198.51.100.1
ping 100.64.1.1
ping 100.64.1.2
ping 100.64.2.1
ping 100.64.2.2
```
Expected:
- successful ICMP replies to all tested addresses

### 4. Verify external reachability from R1
Run:
```cisco
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute prefers the primary WAN path through ISP1

### 5. Verify end-host reachability from PC1
From PC1 command prompt:
```cisco
ping 192.168.10.1
ping 8.8.8.1
ping 8.8.8.8
```
Expected:
- default gateway reachable
- external network reachable
- external server reachable

### 6. Verify end-host reachability from SRV1
From the SRV1 command prompt, run:
```cisco
ping 192.168.20.1
ping 8.8.8.1
ping 8.8.8.8
```

Expected:
- default gateway reachable
- external network reachable
- external server reachable

### Result
Baseline validation was successful. R1 preferred ISP1 as the active default route, internal endpoints reached the external server successfully, and router-originated reachability to upstream transit networks and the external destination was confirmed.


### Evidence to Capture
- `show ip interface brief` on R1
- `show ip route` on R1
- successful `ping 8.8.8.8` from R1
- successful `traceroute 8.8.8.8` from R1
- successful `ping 8.8.8.8` from PC1
- successful `ping 8.8.8.8` from SRV1

