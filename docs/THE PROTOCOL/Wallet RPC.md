# Wallet RPC

## Introduction

This document provides detailed descriptions of the Remote Procedure Call (RPC) commands available in the Salvium wallet. These RPC commands allow developers and users to interact programmatically with a Salvium wallet, enabling a wide range of operations such as querying balances and sending transactions.

## General Structure

Each RPC command has the following general structure:

* **HTTP Method**: Typically POST.
* **Endpoint**: `/json_rpc`
* **Request Format**: JSON
* **Response Format**: JSON

A typical request will include the method name, parameters (if any), and a unique identifier for the request. The response will include the result or an error message.

### Example Request:

```json
{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "<RPC method>",
    "params": {
        // Parameters (if any)
    }
}
```

### Example Response:

```json
{
    "jsonrpc": "2.0",
    "id": "0",
    "result": {
        // Result data
    },
    "error": {
        // Error details (if any)
    }
}
```

## Available RPC Commands

### Get Balance

**Description:** Returns the balance of the wallet for a specified asset type. Optionally, it can return the balance for all assets.

#### Request:

* **Method:** `get_balance`
* **Parameters:**
  * `asset_type` (string, required): The asset type to query.
  * `all_assets` (boolean, optional): If true, returns balances for all assets. Defaults to false.
  * `account_index` (unsigned int, optional): Return balance of this account index.
  * `address_indices` (array of unsigned int, optional): Return balance of addresses with these indices.

#### Response:

```json
{
    "balance": <balance>,
    "unlocked_balance": <unlocked_balance>,
    "multisig_import_needed": <boolean>,
    "per_subaddress": [
        {
            "address_index": <index>,
            "address": "<address>",
            "balance": <balance>,
            "unlocked_balance": <unlocked_balance>,
            "label": "<label>",
            "num_unspent_outputs": <outputs>,
            "asset_type": "<asset_type>"
        }
    ],
    "status": "<status>"
}
```

### Transfer

**Description:** Sends assets from the wallet to a specified destination. This method supports multiple asset types and transaction types.

#### Request:

* **Method:** `transfer`
* **Parameters:**
  * `destinations` (array of objects, required): Each destination requires an address, amount, and asset_type.
  * `source_asset` (string, required): The source asset type.
  * `dest_asset` (string, required): The destination asset type.
  * `tx_type` (enum, required): The type of transaction, which can be one of the following values:
    * `UNSET` (0)
    * `MINER` (1)
    * `PROTOCOL` (2)
    * `TRANSFER` (3)
    * `CONVERT` (4)
    * `BURN` (5)
    * `STAKE` (6)
    * `RETURN` (7)
    * `MAX` (7)
  * `account_index` (unsigned int, optional): Transfer from this account index.
  * `subaddr_indices` (array of unsigned int, optional): Transfer from these subaddresses.
  * `ring_size` (unsigned int, optional): Number of outputs used to mix the transaction.
  * `get_tx_key` (boolean, optional): Return the transaction key after sending.
  * `do_not_relay` (boolean, optional): If true, do not relay the transaction to the network.
  * `get_tx_hex` (boolean, optional): Return the transaction as hex after sending.
  * `get_tx_metadata` (boolean, optional): Return the transaction metadata after sending.

#### Response:

```json
{
    "tx_hash": "<transaction_hash>",
    "tx_key": "<transaction_key>",
    "amount": <amount>,
    "fee": <fee>,
    "tx_blob": "<transaction_blob>",
    "status": "<status>"
}
```

### Transfer Split

**Description:** Splits the transfer into multiple transactions to avoid exceeding size limits. Supports multiple asset types and transaction types.

#### Request:

* **Method:** `transfer_split`
* **Parameters:**
  * `destinations` (array of objects, required): Each destination requires an address, amount, and asset_type.
  * `source_asset` (string, required): The source asset type.
  * `dest_asset` (string, required): The destination asset type.
  * `tx_type` (enum, required): The type of transaction, which can be one of the following values:
    * `UNSET` (0)
    * `MINER` (1)
    * `PROTOCOL` (2)
    * `TRANSFER` (3)
    * `CONVERT` (4)
    * `BURN` (5)
    * `STAKE` (6)
    * `RETURN` (7)
    * `MAX` (7)
  * `account_index` (unsigned int, optional): Transfer from this account index.
  * `subaddr_indices` (array of unsigned int, optional): Transfer from these subaddresses.
  * `ring_size` (unsigned int, optional): Number of outputs used to mix the transaction.
  * `get_tx_key` (boolean, optional): Return the transaction key after sending.
  * `do_not_relay` (boolean, optional): If true, do not relay the transaction to the network.
  * `get_tx_hex` (boolean, optional): Return the transaction as hex after sending.
  * `get_tx_metadata` (boolean, optional): Return the transaction metadata after sending.

#### Response:

```json
{
    "tx_hash_list": [ "<transaction_hash>", ... ],
    "tx_key_list": [ "<transaction_key>", ... ],
    "amount_list": [ <amount>, ... ],
    "fee_list": [ <fee>, ... ],
    "tx_blob_list": [ "<transaction_blob>", ... ],
    "status": "<status>"
}
```

### Sweep All

**Description:** Sends all unlocked outputs to a specified address. Supports sweeping specific asset types.

#### Request:

* **Method:** `sweep_all`
* **Parameters:**
  * `address` (string, required): The destination address.
  * `asset_type` (string, required): The asset type to sweep.
  * `account_index` (unsigned int, optional): Sweep from this account index.
  * `subaddr_indices` (array of unsigned int, optional): Sweep from these subaddresses.
  * `ring_size` (unsigned int, optional): Number of outputs used to mix the transaction.
  * `do_not_relay` (boolean, optional): If true, do not relay the transaction to the network.
  * `get_tx_hex` (boolean, optional): Return the transaction as hex after sending.
  * `get_tx_metadata` (boolean, optional): Return the transaction metadata after sending.

#### Response:

```json
{
    "tx_hash_list": [ "<transaction_hash>", ... ],
    "tx_key_list": [ "<transaction_key>", ... ],
    "amount_list": [ <amount>, ... ],
    "fee_list": [ <fee>, ... ],
    "status": "<status>"
}
```

### Sweep Single

**Description:** Sends a single output to a specified address. Supports sweeping a specific asset type.

#### Request:

* **Method:** `sweep_single`
* **Parameters:**
  * `key_image` (string, required): The key image of the output to sweep.
  * `asset_type` (string, required): The asset type to sweep.
  * `address` (string, required): The destination address.
  * `ring_size` (unsigned int, optional): Number of outputs used to mix the transaction.
  * `do_not_relay` (boolean, optional): If true, do not relay the transaction to the network.
  * `get_tx_hex` (boolean, optional): Return the transaction as hex after sending.
  * `get_tx_metadata` (boolean, optional): Return the transaction metadata after sending.

#### Response:

```json
{
    "tx_hash": "<transaction_hash>",
    "tx_key": "<transaction_key>",
    "amount": <amount>,
    "fee": <fee>,
    "tx_blob": "<transaction_blob>",
    "status": "<status>"
}
```

### Get Transfers

**Description:** Returns a list of transfers, filtered by asset type among other parameters.

#### Request:

* **Method:** `get_transfers`
* **Parameters:**
  * `in` (boolean, optional): Include incoming transfers.
  * `out` (boolean, optional): Include outgoing transfers.
  * `pending` (boolean, optional): Include pending transfers.
  * `failed` (boolean, optional): Include failed transfers.
  * `pool` (boolean, optional): Include pool transfers.
  * `filter_by_height` (boolean, optional): Filter transfers by block height.
  * `min_height` (unsigned int, optional): Minimum block height to include.
  * `max_height` (unsigned int, optional): Maximum block height to include.
  * `account_index` (unsigned int, optional): Filter transfers for this account.
  * `subaddr_indices` (array of unsigned int, optional): Filter transfers for these subaddresses.

#### Response:

```json
{
    "in": [ /* array of incoming transfers */ ],
    "out": [ /* array of outgoing transfers */ ],
    "pending": [ /* array of pending transfers */ ],
    "failed": [ /* array of failed transfers */ ],
    "pool": [ /* array of pool transfers */ ],
    "transfer_entry": {
        "txid": "<transaction_id>",
        "payment_id": "<payment_id>",
        "height": <height>,
        "timestamp": <timestamp>,
        "amount": <amount>,
        "fee": <fee>,
        "note": "<note>",
        "asset_type": "<asset_type>"
    },
    "status": "<status>"
}
```