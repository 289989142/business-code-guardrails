# Synthetic Business-Rule Scenarios

These scenarios and identifiers are invented for illustration. They are not
production incidents, provider contracts, universal business rules, or measured
model results. Apply the reasoning, not the scenario's policy, to another system.

## 1. Cancellation Before Collection

**Request:** Allow customer cancellation before the carrier collects the parcel.

**Available evidence:**

- A stored order enum has `PAID=40`, `COLLECTED=15`, and `COMPLETED=80`.
- A carrier callback sets `collectedAt` after an authenticated collection event.
- The cancellation service is shared by ordinary goods and a service product.

**Unsupported shortcut:** Use `state < COLLECTED`, or apply the service product's
rule to every order. The numeric encoding is not a chronological order.

**Expected approach:** Locate the confirmed signal for collection, the intended
product scope, and all relevant cancellation entry points. Check how a concurrent
collection callback affects cancellation. Preserve the existing behavior for
other product types. If event semantics are missing, ask for that mapping before
implementing the eligibility decision.

**Verification targets:** Eligible pre-collection cancellation, rejected
post-collection cancellation, the relevant race, and unchanged ordinary-goods
behavior. These targets do not prescribe a new locking architecture.

## 2. Settlement After Completion

**Request:** Start settlement when an order is completed.

**Available evidence:**

- Completion is delivered through a callback that may be retried.
- Local settlement records distinguish `PENDING`, `PROCESSING`, and `SUCCEEDED`.
- A request timeout does not establish whether the provider accepted the transfer.
- The provider's idempotency and result-query contract has not been supplied.

**Unsupported shortcut:** Call the transfer API on every completion event, then
mark success merely because the request was sent.

**Expected approach:** Find existing duplicate-event handling and settlement
attempt tracking. Distinguish business completion from settlement success. Ask
for the provider's idempotency and query guarantees before designing automatic
retries after an ambiguous timeout. Continue inspecting local code without
transferring real funds or inventing a provider guarantee.

**Verification targets:** Duplicate completion, accepted-but-timed-out requests,
confirmed transfer failures, and unchanged unrelated payment paths. The exact
recovery strategy depends on the confirmed provider contract.

## 3. Third-Party Status Mapping

**Request:** Map a fulfillment provider's status into the existing order model.

**Available evidence:**

- A versioned contract defines structured codes `C1=collected` and `D1=delivered`.
- A response contains `statusCode=C1` and `message="operation completed"`.
- An existing application enum uses different numeric values for its own states.

**Unsupported shortcut:** Search the message for "completed" and mark the order
completed, or reuse the provider's codes as local persisted enum values.

**Expected approach:** Map the applicable structured code according to the
contract, preserve the local encoding, and check which side effects each local
transition triggers. If an unknown code arrives, do not infer success from the
message. Follow an existing confirmed unknown-status policy, or raise the missing
decision before introducing one.

**Verification targets:** Known mappings, unknown codes, duplicate events, and
out-of-order delivery where the provider contract permits it.

## A Useful Final Report

The exact format can vary. A compact report should make these distinctions clear:

- Confirmed rule and its requirement or contract source.
- Current implementation and the intentionally changed behavior.
- Relevant historical consumers or data that must remain compatible.
- Material unknowns, if any, and the behavior they block.
- Verification actually performed, plus anything not run under the user's limits.

Do not report these illustrative verification targets as completed tests.
