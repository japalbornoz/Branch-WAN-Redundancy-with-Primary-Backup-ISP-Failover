# Baseline Validation

## Objective
Confirm that the branch WAN redundancy topology is operating normally with the **primary ISP path active** before failure testing begins.

## Expected State
- R1 prefers **ISP1** as the active default route
- PC1 can reach the external server
- SRV1 can reach the external server
- R1 can reach upstream transit interfaces and the external server
- Traceroute follows the primary WAN path

## Devices and Paths Checked
- R1
- ISP1
- ISP2
- INET
- PC1
- SRV1
- EXT-SRV

## Validation Steps

### 1. Verify interface status on R1
Run:
```
show ip interface brief
```
Expected:
- G0/0 up/up
- G0/1 up/up
- S0/0/0 up/up
- S0/0/1 up/up

### 2. Verify R1 routing table  
Run:
```
show ip route
```
Expected active default route:
```
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```
This confirms that ISP1 is the preferred outbound path.

### 3. Verify local WAN reachability from R1
Run:
```
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
```
ping 8.8.8.8
traceroute 8.8.8.8
```
Expected:
- ping succeeds
- traceroute follows the ISP1 path

### 5. Verify end-host reachability from PC1
From PC1 command prompt:
```
ping 192.168.10.1
ping 8.8.8.1
ping 8.8.8.8
```
Expected:
- default gateway reachable
- external network reachable
- external server reachable

### 6. Verify end-host reachability from SRV1
From SRV1 command prompt:
```
ping 192.168.20.1
ping 8.8.8.1
ping 8.8.8.8
```

Expected:
- default gateway reachable
- external network reachable
- external server reachable

### Result
Baseline validation was successful. The branch router preferred ISP1 as the active default route, internal endpoints reached the external server successfully, and R1 confirmed upstream and external reachability.


### Evidence to Capture
- show ip interface brief on R1
- show ip route on R1
- successful ping 8.8.8.8 from R1
- successful traceroute 8.8.8.8 from R1
- successful ping 8.8.8.8 from PC1
- successful ping 8.8.8.8 from SRV1

