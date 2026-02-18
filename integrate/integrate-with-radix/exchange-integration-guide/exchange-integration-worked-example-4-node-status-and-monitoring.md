---
title: "Worked example 4: Node status and monitoring"
---
# Worked example 4: Node status and monitoring

You can use the following endpoints to monitor status:

-   Endpoints on the [System API or Prometheus Metrics API](node-setting-up-grafana)
-   Sync status can also be investigated via:
    -   The `ledger_clock` from `/lts/transaction/construction` can tell you how far the node’s ledger tip (ie, the `ledger_clock`) is behind the current time. This can be useful for tracking sync-up. Assuming the network is currently live, this will give you how far the node is behind the network