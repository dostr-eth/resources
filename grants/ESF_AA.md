# DOSTR 📡

## Abstract
Dostr ('Dost' means 'friend' in Hindi) is a fully backward-compatible Nostr client with Ethereum integration. It's a collection of experimental specifications and implementations of Ethereum-aware Nostr clients that enable transactions and transfer of signed data among users and services. Dostr client allows Bitcoin-native Nostr network to function with Ethereum wallets and leverage properties of Ethereum-native protocols, such as Sign-In With Ethereum (`SIWE`), allowing users to import their ENS identity/records and make ERC stealth payments to each other. Dostr is unsurprisingly a minimal implementation of 'Sign-In With X' (`SIWX`).

## Objectives
Dostr's first objective of bridging the identity layers of Nostr network and the Ethereum ecosystem through its v1 client has already been accomplished. [Dostr v1 client](https://dostr.eth.limo) already implements Sign-In With Ethereum (`SIWE`) and ENS intergation on its platform. In the immediate future, Dostr should see ERC stealth payments enabled on its client along with Lightning BTC payments to ENS names. Further beyond, success of the client and its contributions to the Ethereum and Nostr ecosystems will be determined by its user base. Objective of Dostr, in nutshell, is to fully integrate the Nostr peer-to-peer networking protocol with an Ethereum-native identity and payments side-stack. This is essentially abstracting Ethereum wallet beyond its expected utility to work in non-Ethereum peer-to-peer networking environments.

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/dostr.png)

## Outcomes
Ethereum was originally designed as a 3-in-1 protocol: Ethereum/EVM as **Consensus** mechanism, Swarm as **Storage** system and Whisper as **Messaging** protocol. Dostr with its unique client infrastrcuture that bootstraps on an already successful networking protocol, is equivalent to Whisper in this grander vision outlined in the Ethereum whitepaper. In fact, the vision outlined for Dostr at the moment far exceeds the utility expected of Whisper. Dostr in its final form expects to route both stealth ERC payments and Lightning BTC transactions to ENS names, in addition to being a peer-to-peer networking protocol.  

## Grant Scope
This grant is foreseen to cover development work on Dostr including but not limited to,

**a)** enabling stealth payments, and

**b)** smoothen and fully automatise ENS-IPFS-Swarm integration.

The team has additional plans to interface/intergate Nostr relays with Chainlink's CCIP gateways to allow pushing transactions to different bundlers and/or mempools, including EIP-4337-like alt mempool. This core account abstraction research however doesn't fall under the scope of this grant and somewhat relies on the adoption of Dostr client. The team will deep dive into this only after the aforementioned goals a) & b) are achieved.

## Project Team
The Dostr team consists of two degen developers based out of India (`sshmatrix.eth`) and Nepal (`0xc0de4c0ffee.eth`). Both are freelance programmers with an extensive skillset and do not count days in hours.

## Background
The team members have collectively built several projects other than Dostr. [`0xc0de4c0ffee.eth`](https://github.com/0xc0de4c0ffee) has an extensive background in cryptography and has been developing on Bitcoin and Ethereum since almost 10 years. [`sshmatrix.eth`](https://sshmatrix.eth.limo) is an astrophysicist-turned-programmer with 10+ academic papers and several crypto projects under his belt.

## Methodology
Dostr v1 client and its infrastructure is detailed in the proposed [NIP-111](https://github.com/dostr-eth/nips/blob/ethkeygen/111.md). This NIP is under review at [`Pull Request #268`](https://github.com/nostr-protocol/nips/pull/268). The associated toolkit to enable Ethereum integration is [available on GitHub](https://github.com/dostr-eth/nostr-tools/tree/sign-in-with-x) and the associated [`Pull Request #132`](https://github.com/nbd-wtf/nostr-tools/pull/132) is also under review.

Dostr v1 at its core is an account abstraction specification in which a cryptographic signature calculated by one signing algorithm and its native keypair (e.g. Bitcoin-native Schnorr algorithm) can be used to derive a deterministic cryptographic keypair for another signing algorithm (e.g. Ethereum-native ECDSA algorithm) using an appropriate singular (non-invertible) key derivation function. Dostr specifically uses HMAC-based Extract-and-Expand Key Derivation Function ([HKDF](https://datatracker.ietf.org/doc/html/rfc5869)) coupled with `sha256`. Key derivation is outlined in detail in [NIP-111 proposal](https://github.com/dostr-eth/nips/blob/ethkeygen/111.md) and can be visually understood as follows:

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/nip-xx.png)

## Timeline
The timeline for the project is simple and set at **6** months to achieve the two goals a) and b) outlined in the Grant Scope section. We expect

- outcome b) to occur first at the end of Q2 2023 and,
- outcome a) to follow after at the end of Q3 2023.

## Budget
**Amount Requested:** $21,000

The costs associated with the grant will be used for primarily three purposes:

- **Principle Developers' Costs**, covering basic cost of living for two developers.
- **DevOps & Digital Infrastructure Costs**, covering protocol publicising, servers, possible on-boarding of a third member etc.
- **Contract Deployment Costs**, for contract deployments on mainnet.

# Links

- [Dostr Client (web2)](https://dostr.xyz)
- [Dostr Client (web3)](https://dostr.eth.limo)
- [Dostr Specification Document (NIP-111)](https://github.com/dostr-eth/nips/blob/ethkeygen/111.md)
- [Dostr Client Source Code](https://github.com/dostr-eth/dostr-client)
- [Dostr Toolkit Source Code](https://github.com/dostr-eth/nostr-tools)
