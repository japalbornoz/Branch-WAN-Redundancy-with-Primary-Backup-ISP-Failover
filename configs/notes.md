# Configuration Notes

## Routing Design
R1 uses a static default route to ISP1 and a floating static backup route to ISP2. This allows the branch router to prefer ISP1 under normal conditions and automatically switch to ISP2 when the primary WAN path is unavailable.

## DCE / DTE
The ISP serial interfaces were configured as the DCE side, while R1 was configured as the DTE side. Clock rate was applied only on the DCE interfaces.

## Upstream Return Routing
During validation, router-originated pings initially failed even though end-host traffic succeeded. The issue was missing return routes for the branch WAN /30 networks on the INET router. Static routes for `203.0.113.0/30` and `198.51.100.0/30` were added on INET to restore full return-path reachability.

## Testing Approach
Validation focused on:
- baseline connectivity
- primary WAN failure
- primary WAN restoration
- backup WAN failure while primary remains active

## Scope Decision
This project intentionally used a simple LAN design with hosts connected directly to R1. The focus of the project was WAN failover behavior, not VLAN segmentation or multilayer switching.
