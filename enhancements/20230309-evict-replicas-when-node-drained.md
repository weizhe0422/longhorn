# Evict Replicas When Node Drained

## Summary

When a node is drained without first evicting pods on that node, the replica becomes orphaned and no new replica is created on another schedulable node. 

With the new `automatically-evict-replica-when-node-drained` setting, Longhorn can trigger eviction when it detects that the node to which the replica belongs is drained. 

### Related Issues

https://github.com/longhorn/longhorn/issues/2235
https://github.com/longhorn/longhorn/issues/2238

## Motivation

### Goals

Introduced a new `automatically-evict-replica-when-node-drained` setting that evicts replicas on deleted nodes to another schedulable node for rebuilding.

### Non-goals [optional]

`None`

## Proposal

1. Having a new setting: `automatically-evict-replica-when-node-drained` with `true` and `false` options
2. In node contoller reconcile, if `automatically-evict-replica-when-node-drained` is `true`, then checks to see if any nodes are drained, and if so, sets `node.spec.evictionRequested` to `true` to let replicas coordinate to trigger replica eviction.
  - Determine the node is drained
    - `nodes.sepc.allowScheduling` in node CR equals `false`
    - All Longhorn workloads except daemons and PDBs have been evicted

### User Stories

When a user attempted to drain a node with `kubectl drain <node-name>` before deleting the node, it used to be that the user had to manually evict the replica to another schedulable node. 

With this setting enabled, drained nodes will trigger automatic eviction of replicas to another schedulable node to maintain volume availability.

### User Experience In Detail

After a user drains a node making it unschedulable and all Longhorn workloads except daemons and PDBs are evicted, it triggers the node eviction mechanism to reschedule the replica to another schedulable node to keep the volume replica count as expectated.

### API changes

## Design

### Implementation Overview

Overview of how the enhancement will be implemented.

### Test plan

Integration test plan.

For engine enhancement, also requires engine integration test plan.

### Upgrade strategy

Anything that requires if the user wants to upgrade to this enhancement.

## Note [optional]

Additional notes.
