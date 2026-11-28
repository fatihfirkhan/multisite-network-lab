# Test Plan
Records the tests run after each phase of the lab, with expected and actual results. "Pass" means the actual result matched the expected result, including tests that are expected to fail.

## Phase 2: Basic Device Setup
| # | Test | Expected | Actual | Result |
|---|---|---|---|---|
| 1 | `show ip interface brief` on routers | Cabled interfaces up | Cabled interfaces up | Pass |
| 2 | `show interfaces status` on switches | Cabled ports connected | Cabled ports connected | Pass |

## Phase 3: VLANs and Trunks (before inter-VLAN routing)

| # | From | To | Expected | Actual | Result |
|---|---|---|---|---|---|
| 1 | HQ-PC-Staff1 | HQ-PC-Staff2 (192.168.10.51) | Success |  | Pass |
| 2 | HQ-SRV1 | HQ-SRV2 (192.168.20.11) | Success | Success | Pass |
| 3 | BR-PC-Staff1 | BR-PC-Staff2 (192.168.40.51) | Success | Success | Pass |
| 4 | BR-SRV1 | BR-SRV2 (192.168.50.11) | Success | Success | Pass |
| 5 | HQ-PC-Staff1 | HQ-SRV1 (192.168.20.10) | Fail (no routing yet) | 100% packet loss | Pass |
| 6 | HQ-PC-Staff1 | HQ-PC-Guest1 (192.168.30.50) | Fail (no routing yet) | 100% packet loss | Pass |
| 7 | VLAN 30 same-VLAN test | n/a | n/a | Not tested: only one device in VLAN 30 | N/A |

## Phase 4: Inter-VLAN Routing
| # | From | To | Expected | Actual | Result |
|---|---|---|---|---|---|
| 1 | HQ-PC-Staff1 | 192.168.10.1 (gateway) | Success | Success | Pass |
| 2 | HQ-PC-Staff1 | HQ-SRV1 (192.168.20.10) | Success | Success | Pass |
| 3 | HQ-PC-Staff1 | HQ-PC-Guest1 (192.168.30.50) | Success | Success | Pass |
| 4 | BR-PC-Staff1 | BR-SRV1 (192.168.50.10) | Success | Success | Pass |
| 5 | HQ-PC-Staff1 | BR-PC-Staff1 (192.168.40.50) | Fail (no link between sites yet) | Success | Pass |

## Phase 5: WAN Links and OSPF
| # | From | To | Expected | Actual | Result |
|---|---|---|---|---|---|
| 1 | HQ-R1 | 10.0.0.2 (ISP-R1) | Success | Success | Pass |
| 2 | ISP-R1 | 10.0.0.6 (BR-R1) | Success | Success | Pass |
| 3 | HQ-PC-Staff1 | BR-SRV1 (192.168.50.10) | Success | Success | Pass |
| 4 | BR-PC-Staff1 | HQ-SRV1 (192.168.20.10) | Success | Success | Pass |
| 5 | HQ-PC-Staff1 | Internet-SRV (203.0.113.10) | Success (before NAT) | Success | Pass |

## Phase 6: NAT
| # | From | To | Expected | Actual | Result |
|---|---|---|---|---|---|
| 1 | HQ-PC-Staff1 | Internet-SRV (203.0.113.10) | Success; translation table shows 10.0.0.1 | Success | Pass |
| 2 | BR-PC-Staff1 | Internet-SRV (203.0.113.10) | Success; translation table shows 10.0.0.6 | Success | Pass |
| 3 | HQ-PC-Staff1 | BR-SRV1 (192.168.50.10) | Success; no NAT entry created | Success | Pass |
