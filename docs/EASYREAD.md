# Dostr: Ethereum-flavoured Nostr
![Icon](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/icon_smol.png)
#### Links: [[Prototype](https://dostr-eth.github.io/homepage/)] [[GitHub](https://github.com/dostr-eth)]

# DOSTR 📡

Dostr ('Dost' means 'friend' in Hindi, an adoption of 'Doost' from Farsi) is an Ethereum-flavoured [Nostr](https://github.com/nostr-protocol/nostr#readme) client. Dostr is a fully backward-compatible Nostr client with ENS and Ethereum integration. It's a collection of experimental specifications and implementations of Ethereum-aware Nostr clients to transfer signed data/messages, and transact between users and services.

Ethereum was originally designed as a 3-in-1 protocol: Ethereum/EVM as **Consensus** mechanism, Swarm as **Storage** system and Whisper as **Messaging** protocol. Dostr is equivalent to Whisper in this grander vision outlined in the Ethereum white paper. Dostr client allows Nostr to function with Ethereum wallets ([SIWE](https://login.xyz/)) and leverage properties of [ENS](https://docs.ens.domains/), such as allowing users to import their ENS identity, avatars and other records on Nostr. Dostr is unsurprisingly a minimal implementation of [Sign In With X (SIWx)](https://chainagnostic.org/CAIPs/caip-122).

## Introduction

To understand Dostr, one must understand Nostr. Nostr is a minimal peer-to-peer networking protocol with following properties:

- Nostr consists of **Clients**  and **Relays**. Clients are local instances run by users and relays are websocket servers which communicate between clients.
- Nostr clients have the ability to talk to relays and make requests for filtered information, which the relay fetches from another client and returns to the original requester.
- Nostr clients are identified by their private keys and may have a **nickname**. Nostr clients must sign the information they send and relays must validate the information before transmitting it.

More details about the Nostr protocol may be found [here](https://github.com/rajarshimaitra/rust-nostr/blob/main/VISION.md) (and [here](https://github.com/nostr-protocol/nips)). In native Nostr implementation, Schnorr signature scheme is used as per Bitcoin-native [BIP-340 standard](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki#design). Dostr adds to this standard by deriving the Nostr-specific private keys from Ethereum-native ECDSA signature scheme outlined in [EIP-191](https://eips.ethereum.org/EIPS/eip-191) and [EIP-712](https://eips.ethereum.org/EIPS/eip-712) using HMAC Key Derivation Function (HKDF). In Dostr clients, ENS names of the signing wallets become the usernames of the users and users may choose to import their ENS avatars and other records.

### Dostr Design

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/dostr.png)

Dostr design is identical to the Nostr specifications described in [NIP-01](https://github.com/nostr-protocol/nips/blob/master/01.md) except for two main differences - **a)** using ECDSA signatures to derive user keys, and **b)** use of ENS via a gateway to access the user properties instead of a web2 DNS server. The detailed [NIP-111 proposal](https://github.com/dostr-eth/nips/blob/ethkeygen/111.md) covering these two aspects is currently [under review](https://github.com/nostr-protocol/nips/pull/268).

## How is Dostr helpful?

Dostr allows Nostr to work with Ethereum-based wallets and leverage the rich UX of Ethereum ecosystem. Dostr replaces the current web2 centralised dependencies in Nostr ecosystem (DNS and server storage) with web3 decentralised dependencies, e.g. ENS and IPFS respectively. In course of this, Dostr has unwittingly solved the problem of secure login into Nostr network from mobile devices. No universal Nostr mobile extensions existed until Dostr (Damus recently launched a Nostr client but for iOS only). In addition, most Ethereum-wallets implement ENS by default and Dostr is the first and only protocol that allows users to utilise their [NIP-02](https://github.com/nostr-protocol/nips/blob/master/02.md) compatible ENS names on Nostr. Dostr further leverages properties of ENS such as its DNS resolution via a gateway [https://vitalik.eth.LIMO](https://vitalik.eth.limo) to store [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible public user information on decentralised storage infrastructures such as **IPFS**, Arweave, Swarm etc.

## Is Dostr secure?

The **Nostr-specific** private keys derived by Dostr (using the HMAC function) are **new** keys which have no invertible connection to the Ethereum wallet that derived it. Users can import or export their new generated Nostr-specific private keys in other Nostr clients and continue to use the same ENS features without exposing their Ethereum wallet private keys.

## Current status

Dostr client UI is ready and the prototype test build can be accessed on [GitHub Pages](https://dostr-eth.github.io/homepage/). Dostr client is a fork of [Astral](https://astral.ninja/) (which is a fork of Branle), and it can already be used with generic Nostr keys. Developers are currently testing [SIWE interfacing](https://github.com/dostr-eth/nips/blob/ethkeygen/111.md) with the UI while NIP-111 is under active review. The expected release date for the client is March 31 2023.
