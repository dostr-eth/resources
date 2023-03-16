# Dostr: Ethereum-flavoured Nostr
![Icon](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/icon_smol.png)
#### Links: [[Prototype](https://dostr-eth.github.io/homepage/)] [[GitHub](https://github.com/dostr-eth)]

# DOSTR 📡

Dostr is an Ethereum-flavoured [Nostr](https://github.com/nostr-protocol/nostr#readme) client. Dostr is an optional feature to Nostr which allows users to generate deterministic Nostr-specific private keys from Ethereum-native ECDSA signatures. This allows Nostr to function with Ethereum wallets (SIWE) and leverage properties of [ENS (Ethereum Name Service)](https://docs.ens.domains/), such as allowing users to import their ENS identity and records on Nostr. Dostr is unsurprisingly a minimal implementation of [Sign In With X (SIWx)](https://chainagnostic.org/CAIPs/caip-122).

## Introduction

To understand Dostr, one must understand Nostr. Nostr is a minimal peer-to-peer networking protocol with following properties:

- Nostr consists of **Clients**  and **Relays**. Clients are local instances run by users and relays are websocket servers which communicate between clients.
- Nostr clients have the ability to talk to relays and make requests for filtered information, which the relay fetches from another client and returns to the original requester.
- Nostr clients are identified by their private keys and may have a **Petname**. Nostr clients must sign the information they send and relays must validate the information before transmitting it.

More details about the Nostr protocol may be found [here](https://github.com/rajarshimaitra/rust-nostr/blob/main/VISION.md) (and [here](https://github.com/nostr-protocol/nips)). In native Nostr implementation, Schnorr signature scheme over `secp256k1` is used as per Bitcoin-native [BIP-340 standard](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki#design). Dostr adds to this standard by deriving the Nostr-specific private keys from Ethereum-native ECDSA signature scheme outlined in [EIP-191](https://eips.ethereum.org/EIPS/eip-191) and [EIP-712](https://eips.ethereum.org/EIPS/eip-712) using HMAC Key Derivation Function (HKDF). In Dostr clients, ENS names of the signing wallets become the usernames of the users.

### Dostr Design

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/dostr.png)

Dostr design is identitical to the Nostr specifications described in [NIP-01](https://github.com/nostr-protocol/nips/blob/master/01.md) except for two main differences - using ECDSA signatures to derive user keys, and use of ENS via a gateway to access the user properties instead of a web2 DNS server. The detailed [NIP-XX proposal](https://github.com/dostr-eth/nips/blob/ethkeygen/xx.md) covering these two aspects is currently [under review](https://github.com/nostr-protocol/nips/pull/268).

## How is Dostr helpful?

Dostr allows Nostr to work with Ethereum-based wallets and leverage the rich UX of Ethereum ecosystem. Firstly, Dostr has solved the problem of secure login into Nostr from mobile devices. No universal Nostr mobile extensions existed until Dostr (Damus recently launched a Nostr client but for iOS only). In addition, most Ethereum-wallets implement ENS by default and Dostr is the first and only protocol that allows users to uitlise their [NIP-02](https://github.com/nostr-protocol/nips/blob/master/02.md) compatible ENS names on Nostr. Dostr further leverages properties of ENS such as its DNS resolution via a gateway (https://vitalik.eth.limo) to store [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible public user information.

## Current status

Dostr client UI is ready and the prototype test build can be accessed on [GitHub Pages](https://dostr-eth.github.io/homepage/). Dostr client is a fork of [Astral](https://astral.ninja/) (which is a fork of Branle), and it can already be used with generic Nostr keys. Developers are currently testing [SIWE interfacing](https://github.com/dostr-eth/nips/blob/ethkeygen/xx.md) with the UI while NIP-XX is under active review. The expected release date for the client is March 31 2023. Please support our effort by voting for us!
