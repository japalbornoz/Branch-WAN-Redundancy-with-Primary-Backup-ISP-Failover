# Lessons Learned

## 1. Default routing alone is not the whole story
A default route is enough to forward unknown destinations, but it does not automatically guarantee correct return-path routing from upstream devices.

## 2. Router-originated traffic and host-originated traffic are different
A network can appear healthy for end users while router-originated tests still fail if source addressing and return routes are not fully accounted for.

## 3. Floating static routes are simple and effective
For a small branch lab, floating static routes provide a clean way to demonstrate WAN failover behavior without requiring dynamic routing.

## 4. Validation should include both routing and reachability
It is not enough to configure routes. A proper test should include:
- interface verification
- routing table checks
- ping validation
- traceroute validation
- failure and recovery testing

## 5. Scope discipline improves project quality
Keeping the LAN simple made the WAN redundancy concept easier to build, validate, troubleshoot, and explain.
