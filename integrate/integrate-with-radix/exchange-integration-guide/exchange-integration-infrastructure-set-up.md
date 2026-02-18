---
title: "Infrastructure set-up"
---
# Infrastructure set-up

Assuming that the Core API satisfies your integration requirements, then we’d recommend running:

-   Two mainnet nodes: A primary node, and a hot back-up, which can be swapped to if the primary node has issues.
-   A testnet node which can be used for testing new integrations or upgrades. This can be taken down when not in use.

To configure a node for production, you have a few options:

-   You can configure an Ubuntu VM to run a node using the NodeCLI - details [found on our documentation site here](node).
-   It is also possible to run the docker image in a container orchestrator - by adapting the [docker compose from the testnet-node configuration](https://github.com/radixdlt/babylon-node/blob/main/testnet-node/docker-compose.yml) for your set-up.
-   You can [compile your own node from source](node) and see [these release notes](https://github.com/radixdlt/babylon-node/releases/) explain about some differences / tweaks to get it working for Babylon.