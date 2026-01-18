# ProofPost Protocol Specification

**Version:** 0.1.0-draft
**Status:** Pre-implementation draft
**Author:** Yousef Ebrahimi

## Abstract

This document specifies ProofPost, an ActivityPub extension protocol for attaching cryptographically verifiable provenance metadata to federated social media posts.

## 1. Introduction

### 1.1 Problem Statement

ActivityPub-based platforms lack a standardized mechanism for: 
- Attaching verifiable evidence to claims
- Cryptographically signing verification metadata
- Federating verification context across instances

### 1.2 Design Goals

1. **Federation-native:** ProofCards federate as standard ActivityPub objects
2. **Cryptographically verifiable:** Ed25519 signatures enable trustless verification
3. **Backwards compatible:** Non-participating instances ignore extended metadata
4. **Decentralized trust:** No central authority required for verification

## 2. Data Model

### 2.1 ProofCard Object

A ProofCard is an ActivityPub object extending the base `Object` type.

**Type:** `https://proofpost.org/ns/v1#ProofCard`

**Required Properties:**

| Property | Type | Description |
|----------|------|-------------|
| `id` | URI | Unique identifier for the ProofCard |
| `type` | String | Must be `ProofCard` |
| `attributedTo` | URI | ActivityPub actor who created the card |
| `created` | DateTime | ISO 8601 timestamp of creation |
| `verificationBundle` | VerificationBundle | Evidence and cryptographic proofs |

### 2.2 VerificationBundle Object

**Required Properties:**

| Property | Type | Description |
|----------|------|-------------|
| `contentHash` | String | SHA-256 hash of verified content (format: `sha256:hexstring`) |
| `hashAlgorithm` | String | Must be `sha256` |
| `captureTimestamp` | DateTime | When the content was captured/verified |
| `signature` | Signature | Creator's cryptographic signature |

**Optional Properties:**

| Property | Type | Description |
|----------|------|-------------|
| `archiveUrl` | URI | Link to archived/preserved content |
| `witnessSignatures` | Array[Signature] | Additional attestations from trusted accounts |
| `contentType` | String | MIME type of verified content |
| `description` | String | Human-readable verification summary |

### 2.3 Signature Object

| Property | Type | Description |
|----------|------|-------------|
| `type` | String | Must be `Ed25519Signature2020` |
| `creator` | URI | ActivityPub actor URI of signer |
| `created` | DateTime | Signature timestamp |
| `signatureValue` | String | Base64-encoded Ed25519 signature |

## 3. Cryptographic Operations

### 3.1 Signing

The signature is computed over a canonical JSON serialization of: 
```
contentHash + captureTimestamp + attributedTo
```

Using the Ed25519 private key associated with the `attributedTo` actor.

### 3.2 Verification

1.  Retrieve the public key from the `creator` actor's ActivityPub profile
2. Reconstruct the canonical signing input
3. Verify the Ed25519 signature against the public key

## 4. Federation

### 4.1 Delivery

ProofCards are delivered via standard ActivityPub S2S protocol: 
- `Create` activity when a new ProofCard is published
- `Announce` activity when a ProofCard is boosted/shared

### 4.2 Discovery

ProofCards attached to a post are linked via the `attachment` property:
```json
{
  "type":  "Note",
  "content": "Thread on election results with verified sources.. .",
  "attachment": [
    {
      "type":  "ProofCard",
      "href": "https://instance.example/proofcards/123"
    }
  ]
}
```

## 5. Security Considerations

### 5.1 Key Management

Actor signing keys should follow ActivityPub HTTP Signatures conventions.  ProofPost does not introduce new key distribution mechanisms.

### 5.2 Replay Prevention

The `captureTimestamp` and `created` fields prevent replay attacks.  Verifiers should reject ProofCards with timestamps significantly deviating from current time.

### 5.3 Content Integrity

The `contentHash` binds the ProofCard to specific content. Any modification to the underlying content invalidates the verification. 

## 6. Future Extensions

- Post-quantum signature algorithms
- Decentralized identifier (DID) integration
- Merkle tree aggregation for batch verifications

---

*This specification is a working draft and subject to revision based on community feedback and implementation experience.*
