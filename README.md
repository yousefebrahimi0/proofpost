# ProofPost

**Federated Verification Metadata Protocol for ActivityPub Networks**

ProofPost is an ActivityPub extension enabling Mastodon, PeerTube, and other federated platforms to attach cryptographically-signed verification metadata to posts—creating decentralized fact-checking infrastructure without central authorities.

## Status

🚧 **Pre-development** — This project is seeking funding through the NGI Fediversity Open Call.

## Problem

The Fediverse lacks a standardized method for attaching verifiable provenance to claims.  When misinformation spreads across instances, there's no protocol-level mechanism for collaborative verification that respects federation principles.

## Solution

ProofPost introduces `ProofCard` objects—structured metadata containing: 
- Content hashes (SHA-256)
- Archive snapshots (links to preserved evidence)
- Cryptographic signatures (Ed25519)
- Optional witness attestations from trusted accounts

These objects federate via standard ActivityPub, allowing any instance to independently verify claim provenance.

## Planned Deliverables

- [ ] Protocol specification (JSON-LD schema extending ActivityStreams)
- [ ] Reference server implementation (Node.js + PostgreSQL)
- [ ] Web UI for creating and validating ProofCards
- [ ] JavaScript client SDK
- [ ] Mastodon integration plugin
- [ ] Docker + NixOS deployment configurations

## Documentation

- [Protocol Specification (Draft)](docs/SPECIFICATION.md)
- [Architecture Overview](docs/ARCHITECTURE.md)
- [Example ProofCard](examples/example-proofcard.json)

## License

Code:  [EUPL-1.2](LICENSE)
Documentation: CC-BY-SA 4.0

## Contact

**Yousef Ebrahimi**
- Email: info [@] yousef [.] uk
- GitHub: [@yousefebrahimi0](https://github.com/yousefebrahimi0)
- LinkedIn: [yousefebrahimi0](https://linkedin.com/in/yousefebrahimi0)

## Acknowledgments

This project is proposed for funding under the NGI Fediversity program, established by the European Commission. 
