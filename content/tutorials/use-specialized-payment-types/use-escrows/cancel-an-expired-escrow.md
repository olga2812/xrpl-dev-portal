---
html: cancel-an-expired-escrow.html
parent: use-escrows.html
blurb: Cancel an expired escrow.
labels:
  - Escrow
  - Smart Contracts
---
# Cancel an Expired Escrow

An escrow in the XRP Ledger is expired when its `CancelAfter` time is lower than the `close_time` of the latest validated ledger. Escrows without a `CancelAfter` time never expire.

## 1. Get the latest validated ledger

Use the [ledger method][] to look up the latest validated ledger and get the `close_time` value.

Request:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/ledger-request-expiration.json' %}
```

<!-- MULTICODE_BLOCK_END -->

Response:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/ledger-response-expiration.json' %}
```

<!-- MULTICODE_BLOCK_END -->

## 2. Look up the escrow

Use the [account_objects method][] and compare `CancelAfter` to `close_time`:

Request:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/account_objects-request-expiration.json' %}
```

<!-- MULTICODE_BLOCK_END -->

Response:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/account_objects-response-expiration.json' %}
```

<!-- MULTICODE_BLOCK_END -->

## 3. Submit EscrowCancel transaction

***Anyone*** can cancel an expired escrow in the XRP Ledger by sending an [EscrowCancel transaction][]. Set the `Owner` field of the transaction to the `Account` of the `EscrowCreate` transaction that created this escrow. Set the `OfferSequence` field to the `Sequence` of the `EscrowCreate` transaction.

**Tip:** If you don't know what `OfferSequence` to use, you can look up the transaction that created the Escrow: call the [tx method][] with the value of the Escrow's `PreviousTxnID` field. In `tx` response, use the `Sequence` value of that transaction as the `OfferSequence` value of the EscrowCancel transaction.

{% include '_snippets/secret-key-warning.md' %} <!--#{ fix md highlighting_ #}-->

Request:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/submit-request-escrowcancel.json' %}
```

<!-- MULTICODE_BLOCK_END -->

Response:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/submit-response-escrowcancel.json' %}
```

<!-- MULTICODE_BLOCK_END -->

Take note of the transaction's identifying `hash` value so you can check its final status when it is included in a validated ledger version.

## 4. Wait for validation

{% include '_snippets/wait-for-validation.md' %} <!--#{ fix md highlighting_ #}-->

## 5. Confirm final result

Use the [tx method][] with the `EscrowCancel` transaction's identifying hash to check its final status. Look in the transaction metadata for a `DeletedNode` with `LedgerEntryType` of `Escrow`. Also look for a `ModifiedNode` of type `AccountRoot` for the sender of the escrowed payment. The `FinalFields` of the object should show the increase in XRP in the `Balance` field for the returned XRP.

Request:

<!-- MULTICODE_BLOCK_START -->

_Websocket_


```json
{% include '_code-samples/escrow/websocket/tx-request-escrowcancel.json' %}
```

<!-- MULTICODE_BLOCK_END -->

Response:

<!-- MULTICODE_BLOCK_START -->

_Websocket_

```json
{% include '_code-samples/escrow/websocket/tx-response-escrowcancel.json' %}
```

<!-- MULTICODE_BLOCK_END -->

In the above example, `r3wN3v2vTUkr5qd6daqDc2xE4LSysdVjkT` is the sender of the escrow, and the increase in `Balance` from 99999**8**9990 drops to 99999**9**9990 drops represents the return of the escrowed 10,000 drops of XRP (0.01 XRP).


## See Also

- **Concepts:**
    - [What is XRP?](what-is-xrp.html)
    - [Payment Types](payment-types.html)
        - [Escrow](escrow.html)
- **Tutorials:**
    - [Send XRP](send-xrp.html)
    - [Look Up Transaction Results](look-up-transaction-results.html)
    - [Reliable Transaction Submission](reliable-transaction-submission.html)
- **References:**
    - [EscrowCancel transaction][]
    - [EscrowCreate transaction][]
    - [EscrowFinish transaction][]
    - [account_objects method][]
    - [tx method][]
    - [Escrow ledger object](escrow-object.html)


<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}			
{% include '_snippets/tx-type-links.md' %}			
{% include '_snippets/rippled_versions.md' %}
