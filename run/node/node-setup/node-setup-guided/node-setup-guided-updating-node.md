---
title: "Updating the node"
---
# Updating the node

### Introduction

When updating your node, care should be taken to ensure the update goes smoothly. These instructions use the `babylonnode` CLI tool for an easy update process.

### 1. Prepare to update

First, if you haven’t already done so, ensure that you have backed up your `node-keystore.ks` key file. This key file contains the private key that determines your node’s unique address. If anything goes wrong in the update process, if you have your key file, you can always reinstall the node from scratch and use it to recover access to your node.

Next, you may want to consider using a backup node to perform a switch to the updated node with minimal interruption (especially if running a validator node) – or to provide a quick recovery if something goes wrong during the update.

### 2. Update your node

**Docker mode**

```bash
babylonnode docker install --update
```

**systemd mode**

```bash
babylonnode systemd install -u
```

1.  The `-u` option specifies that this launch of the node will be an update, causing `babylonnode` to create a backup of the current configuration file and ensure that the node has stopped before applying the changes.
    
2.  **Optional** - The -r specifies the release of the node software you wish to install. If not provided it will use the latest release from [https://github.com/radixdlt/babylon-node/releases](https://github.com/radixdlt/babylon-node/releases)
    
3.  **Optional** - The `-i` option is the external IP address of your server.
    

## More information on using babylonnode

You can use `babylonnode` to carry out a variety of administrative tasks.

For a full reference on the commands the `babylonnode` CLI offers, please see the [documentation on the CLI GitHub repository](https://github.com/radixdlt/babylon-nodecli/blob/main/docs/command_reference.adoc).

If you have any questions or run into problems, you can get support from the Radix team members and our fantastic community on our Discord server.