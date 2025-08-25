
# Stacks-BlockCheck – Decentralized KYC Verification Platform

Stacks-BlockCheck is a decentralized Know-Your-Customer (KYC) verification platform built using Clarity for the Stacks blockchain. It allows users to request KYC verification, and authorized verifiers (contract owners) to approve, reject, or revoke verification status for any address in a secure and auditable way.

---

## 📜 Table of Contents

* [Overview](#overview)
* [Features](#features)
* [Verification Lifecycle](#verification-lifecycle)
* [Data Model](#data-model)
* [Error Codes](#error-codes)
* [Functions](#functions)

  * [Public Functions](#public-functions)
  * [Read-Only Functions](#read-only-functions)
  * [Private Functions](#private-functions)
* [Deployment](#deployment)
* [Security Considerations](#security-considerations)
* [License](#license)

---

## 📘 Overview

KYCrypt manages KYC statuses on-chain, maintaining a transparent registry of verified and pending users. The contract owner (admin) is responsible for verifying or rejecting KYC submissions. The design emphasizes security, clarity, and proper input validation.

---

## ✅ Features

* Users can submit KYC data for verification.
* Contract owner can verify, reject, revoke KYC requests.
* Ownership transfer is supported securely.
* Input validation prevents unauthorized or malformed actions.
* Fully decentralized verification registry.

---

## 🔄 Verification Lifecycle

| Status Code | Meaning      | Action Source     |
| ----------- | ------------ | ----------------- |
| `0`         | Not Verified | Default / Revoked |
| `1`         | Pending      | User Request      |
| `2`         | Verified     | Owner Approval    |
| `3`         | Rejected     | Owner Rejection   |

---

## 🧠 Data Model

### `verified-addresses` (Map)

Maps a user address (`principal`) to their KYC verification data:

* `status: uint` – Verification status (0–3)
* `timestamp: uint` – Block height at which the status was updated
* `kyc-data: (string-utf8 500)` – KYC information
* `verifier: principal` – Address of the verifier

### `contract-owner` (Data Variable)

Holds the current contract owner (`principal`) who has elevated privileges.

---

## 🚨 Error Codes

| Code   | Meaning                       |
| ------ | ----------------------------- |
| `u100` | Unauthorized action           |
| `u101` | Already verified or requested |
| `u102` | Not verified                  |
| `u103` | Invalid status transition     |
| `u104` | Invalid input format          |
| `u105` | Invalid new contract owner    |

---

## ⚙️ Functions

### 🔓 Public Functions

#### `(request-verification kyc-data)`

Request KYC verification by submitting `kyc-data`.

* ✅ Only works if status is `0` (not verified).
* ⛔ Errors: `ERR_ALREADY_VERIFIED`, `ERR_INVALID_INPUT`

---

#### `(verify-address address)`

Approve a pending verification (`status = 1`) and mark as verified (`status = 2`).

* ✅ Only callable by contract owner.
* ⛔ Errors: `ERR_UNAUTHORIZED`, `ERR_INVALID_STATUS`

---

#### `(reject-verification address)`

Reject a pending KYC request (`status = 1`) and set status to `3`.

* ✅ Only callable by contract owner.
* ⛔ Errors: `ERR_UNAUTHORIZED`, `ERR_INVALID_STATUS`

---

#### `(revoke-verification address)`

Revert a `verified` or `pending` user back to `not verified`.

* ✅ Only callable by contract owner.
* ⛔ Errors: `ERR_UNAUTHORIZED`, `ERR_INVALID_STATUS`

---

#### `(transfer-ownership new-owner)`

Transfer contract ownership to another principal.

* ✅ Requires current owner and valid new owner.
* ⛔ Errors: `ERR_UNAUTHORIZED`, `ERR_INVALID_NEW_OWNER`

---

### 👀 Read-Only Functions

#### `(get-verification-status address)`

Get the current verification status and KYC data of any address.

---

#### `(is-contract-owner address)`

Returns `true` if the address is the contract owner.

---

### 🔐 Private Functions

Used internally for:

* Address input validation
* KYC data validation
* Status transition validation
* Owner-only action enforcement

---

## 🚀 Deployment

1. Deploy the contract to the Stacks blockchain using Clarinet or directly via Stacks CLI.
2. Initialize with the deploying principal as `contract-owner`.
3. Use `transfer-ownership` to assign admin rights as needed.

---

## 🔒 Security Considerations

* Only the contract owner can manage KYC approvals.
* Input data is validated to prevent empty or malformed submissions.
* `tx-sender` and contract self-calls are disallowed for key actions.
* Ownership transfer includes validation to avoid setting the same owner again.

---

## 📄 License

MIT License — Open for use, modification, and distribution with attribution.

---
