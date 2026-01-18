# ProofPost Architecture Overview

## System Components

```
┌─────────────────────────────────────────────────────────────┐
│                     Fediverse Instance                       │
│  (Mastodon / PeerTube / Pleroma / etc.)                     │
└─────────────────────┬───────────────────────────────────────┘
                      │ ActivityPub S2S
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                   ProofPost Server                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ ActivityPub │  │ Signing     │  │ Verification        │  │
│  │ Endpoint    │  │ Service     │  │ Service             │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ REST API    │  │ Archive     │  │ PostgreSQL          │  │
│  │             │  │ Connector   │  │ Database            │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                    Web Verification UI                       │
│         (Create / Validate / Browse ProofCards)             │
└─────────────────────────────────────────────────────────────┘
```

## Component Responsibilities

### ProofPost Server

**ActivityPub Endpoint**
- Receives federated ProofCard objects
- Sends ProofCards to remote instances
- Handles actor key retrieval for verification

**Signing Service**
- Generates Ed25519 key pairs for actors
- Signs VerificationBundles
- Manages key rotation

**Verification Service**
- Validates incoming ProofCard signatures
- Checks content hashes against archived content
- Returns verification status

**Archive Connector**
- Integrates with Internet Archive Wayback Machine
- Supports local archiving for self-hosted instances
- Retrieves archived content for hash comparison

**REST API**
- `/api/v1/proofcards` — CRUD operations for ProofCards
- `/api/v1/verify` — Signature verification endpoint
- `/api/v1/archive` — Content archiving requests

### Web Verification UI

- Form for creating new ProofCards
- Drag-and-drop evidence upload
- Real-time signature verification display
- ProofCard browser/search

## Technology Stack

| Component | Technology | Rationale |
|-----------|------------|-----------|
| Server Runtime | Node.js 20 LTS | Mature ActivityPub libraries available |
| Language | TypeScript | Type safety for protocol implementation |
| Database | PostgreSQL 15 | JSON support, proven reliability |
| Cryptography | libsodium | Audited Ed25519 implementation |
| Web UI | Vanilla JS + HTML | Minimal dependencies, maximum compatibility |
| Deployment | Docker + NixOS | Reproducible builds, easy self-hosting |

## Data Flow:  Creating a ProofCard

```
1. User submits evidence (screenshot, URL, document)
         │
         ▼
2. Server generates SHA-256 hash of content
         │
         ▼
3. Server optionally submits to archive service
         │
         ▼
4. Server constructs VerificationBundle
         │
         ▼
5. Signing Service signs bundle with actor's Ed25519 key
         │
         ▼
6. Server wraps in ProofCard ActivityPub object
         │
         ▼
7. Server delivers via ActivityPub to followers/instances
```

## Data Flow: Verifying a ProofCard

```
1. Instance receives ProofCard via ActivityPub
         │
         ▼
2.  Verification Service extracts signature + creator URI
         │
         ▼
3. Service fetches creator's public key from origin instance
         │
         ▼
4. Service reconstructs canonical signing input
         │
         ▼
5. Service verifies Ed25519 signature
         │
         ▼
6. Service optionally fetches archived content, compares hash
         │
         ▼
7. Returns verification result (valid / invalid / unknown)
```

## Deployment Models

### Standalone Server
- Full ProofPost server with own database
- Federates with any ActivityPub instance
- Suitable for organizations running verification services

### Mastodon Plugin
- Lightweight integration with existing Mastodon instance
- Delegates to external ProofPost server or runs embedded
- Lowest barrier to adoption

### Self-Contained (Docker)
- Single `docker-compose.yml` brings up complete stack
- PostgreSQL + ProofPost Server + Web UI
- Ideal for testing and small deployments

### NixOS Module
- Declarative configuration for reproducible deployment
- Integrates with existing NixOS-based Fediverse hosting
- Preferred for production environments prioritizing reproducibility
