# Issues Encountered

## 1. Router-originated ping failed while host traffic worked
### Symptom
PC1 could reach the external server, but R1 could not consistently ping `8.8.8.8` or some upstream transit interfaces.

### Cause
INET did not initially have return routes for the branch WAN /30 networks:
- `203.0.113.0/30`
- `198.51.100.0/30`

### Fix
Added static routes on INET for the branch WAN transit subnets.

---

## 2. DCE / DTE serial consideration
### Symptom
Serial links required clocking to come up properly.

### Cause
Clock rate must be configured only on the DCE side.

### Fix
Configured ISP serial interfaces as DCE and applied `clock rate 64000` on those interfaces.

---

## 3. Transit ping behavior changed during failover
### Symptom
Some upstream IP ping tests behaved differently depending on which ISP path was currently active.

### Cause
R1 used the active default route unless a more specific route existed. Router-originated traffic followed the currently preferred path.

### Fix
Validated path behavior using routing table checks and failover-aware testing rather than relying only on single-interface ping assumptions.
