---
name: Run a train work order
description: List work orders and their tasks, then arrive/depart trains and pick up or set out cars in Cedar ARMS.
api: openapi/cedarai-work-order-openapi.yml
operations: [ListWorkOrders, ListWorkOrderTasks, ArriveTrain, DepartTrain, PickupCars, SetoutCars, RecordTrainEvent, ReSequenceTrain]
---

# Run a train work order

Operate a train against a Cedar ARMS work order.

## Auth
POST to the ARMS gRPC-HTTP host (`api-lg-k-h1.arms.cedarai.com` US / `.se` EU) with `x-arms-api-key` + `x-arms-assume-user` headers and the `carrierId` query parameter. See `authentication/cedarai-authentication.yml`.

## Steps
1. `ListWorkOrders` to find the active/pending work order.
2. `ListWorkOrderTasks` to see the pickup/setout tasks for that work order.
3. `ArriveTrain` when the train reaches a customer/serving location; `DepartTrain` when it leaves a station.
4. `PickupCars` and `SetoutCars` to execute the consist changes — on success these complete the matching Pickup/Setout tasks.
5. `RecordTrainEvent` to log a train event; `ReSequenceTrain` to reorder an active train (use `UpdateWorkOrder` for editing a pending train's consist).

## Rules
- Resequencing is for active trains; editing a pending consist uses the work-order update path, not `ReSequenceTrain`.
- Handle 401/403 auth outcomes and 500 with retry+backoff (`errors/cedarai-problem-types.yml`).
