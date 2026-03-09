# Lessons Learned

## 1. Default routing alone does not guarantee full connectivity
A default route is sufficient to forward traffic toward unknown destinations, but it does not automatically guarantee that return traffic from upstream devices will follow the correct path.

During this lab, the upstream INET router required explicit static routes for the branch LAN and WAN subnets. Without proper return-path routing, packets could reach the destination but responses could not return successfully.

This highlights the importance of considering **both forward and return paths** when designing and validating WAN connectivity.

---

## 2. Router-originated traffic and host-originated traffic behave differently
A network can appear healthy for end users while router-originated tests behave differently.

Host traffic uses the host’s default gateway and follows the network forwarding path, while router-originated traffic uses the router’s own routing table and source interface selection. Because of this difference, router-based tests may reveal issues that are not immediately visible from end-host connectivity tests.

Understanding this distinction is important when performing network troubleshooting.

---

## 3. Floating static routes provide simple WAN failover
Floating static routes offer a simple and predictable method for implementing WAN redundancy.

By configuring a secondary default route with a higher administrative distance, the router automatically prefers the primary ISP path while keeping the backup path available if the primary link fails.

For small environments or lab scenarios, this approach provides a clear and easy-to-understand failover mechanism without introducing the complexity of dynamic routing protocols.

---

## 4. Static routing has limitations in failure detection
Static routes do not automatically detect failures beyond the directly connected interface.

In this project, upstream routers were unaware of downstream failures unless the interface they were directly connected to went down. This limitation required careful design of return routes to prevent asymmetric routing during failover testing.

In production environments, mechanisms such as:

- dynamic routing protocols
- IP SLA with object tracking
- provider-side routing policies

are often used to detect upstream reachability and make more intelligent failover decisions.

---

## 5. Effective validation requires multiple verification methods
Proper network validation should not rely on a single command or test.

A structured validation approach should include:
- interface status verification
- routing table inspection
- end-to-end ping testing
- traceroute path verification
- failure simulation and recovery testing

Using multiple validation methods helps confirm both connectivity and correct traffic forwarding behavior.

---

## 6. Keeping the lab scope focused improves clarity
The LAN portion of this project was intentionally kept simple so the focus remained on WAN behavior.

This allowed the redundancy design, routing behavior, failover events, and troubleshooting process to be easier to observe and document. Keeping the scope focused helps produce clearer technical explanations and more effective validation documentation.
