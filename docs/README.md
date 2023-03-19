![Banner](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/banner.png)
# Dostr Technical Design
![Icon](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/icon_smol.png)
#### Authors:`sshmatrix`, `c0de4c0ffee`
#### Links: [[GitHub](https://github.com/dostr-eth)]  [[.ETH](https://dostr.eth.limo)]  [[.XYZ](https://dostr.xyz)] [[Goerli]()]
###### tags: `specification` `design` `architecture` `nostr` `dostr`
# DOSTR 📡

## Abstract

Dostr is an Ethereum-flavoured [Nostr](https://github.com/nostr-protocol/nostr#readme) client. Dostr is an optional Ethereum-native side-stack to Nostr which allows users to generate deterministic Nostr-specific private keys from Ethereum-native ECDSA signatures. This allows Nostr to function with Ethereum wallets and leverage properties of [ENS (Ethereum Name Service)](https://docs.ens.domains/) protocol in Dostr client infrastructure and vice-versa. Consequently, Dostr provides an optional side-stack of Ethereum-based services for Nostr users, e.g. allows them to use their ENS identity on Nostr or attach to [Helix2](https://github.com/helix-coupler/resources/blob/master/yellow-paper/README.md) as a submodule hook. Alternatively, Ethereum users may find that Dostr is a minimal implementation of [Sign In With X (SIWx)](https://chainagnostic.org/CAIPs/caip-122) - an alternative to SIWE.

## Introduction

Dostr is a Nostr client with Ethereum-based side-stack. To understand Dostr, one must understand Nostr. Nostr is a minimal peer-to-peer networking protocol with following properties:

- Nostr consists of **Clients**  and **Relays**. Clients are local instances run by users and relays are websocket servers which communicate between clients.
- Nostr clients have the ability to talk to relays and make requests for filtered information, which the relay fetches from another client and returns to the original requester.
- Nostr clients are identified by their private keys and may have a **Petname**. Nostr clients must sign the information they send and relays must validate the information before transmitting it.

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/nostr.png)

More details about the Nostr protocol may be found [here](https://github.com/rajarshimaitra/rust-nostr/blob/main/VISION.md) (and [here](https://github.com/nostr-protocol/nips)). In native Nostr implementation, Schnorr signature scheme over `secp256k1` is used as per Bitcoin-native [BIP-340 standard](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki#design). Dostr adds to this standard by deriving the Nostr-specific private keys from Ethereum-native ECDSA signature scheme outlined in [EIP-191](https://eips.ethereum.org/EIPS/eip-191) and [EIP-712](https://eips.ethereum.org/EIPS/eip-712) using HMAC Key Derivation Function (HKDF). In Dostr clients, ENS names of the signing wallets become the petnames of the users.

Implementing an Ethereum-based side-stack to Nostr has several utilities. Firstly, it allows Nostr to work with Ethereum-based wallets and leverage the rich UX of Ethereum ecosystem. For instance, most Ethereum-wallets implement ENS by default and Dostr is the first and only protocol that allows users to uitlise their [NIP-02](https://github.com/nostr-protocol/nips/blob/master/02.md) compatible ENS names on Nostr. Dostr further leverages properties of ENS such as its DNS resolution via a gateway (https://vitalik.eth.limo) to store [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible public client information. The cross-chain utility of Dostr is not limited to ENS and extends beyond to other Ethereum-native naming and linking protocols such as (https://github.com/helix-coupler/resources/blob/master/yellow-paper/README.md) or [Woolball](https://woolball.xyz).

## Protocol Design

Dostr design is identitical to the Nostr specifications described in [NIP-01](https://github.com/nostr-protocol/nips/blob/master/01.md) except for two main differences -  using ECDSA signatures to derive user keys, and use of ENS via a gateway to access the user properties instead of a web2 DNS server. The detailed [NIP-XX proposal](https://github.com/dostr-eth/nips/blob/ethkeygen/xx.md) covering these two aspects is currently [under review](https://github.com/nostr-protocol/nips/pull/268). In summary, the Dostr implementation can be summarised with the following pseudo-code.

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/dostr.png)

### Private-key derivation

Dostr uses [HMAC-based Extract-and-Expand Key Derivation Function (HKDF)](https://datatracker.ietf.org/doc/html/rfc5869) coupled with `sha256`. Key derivation is outlined in detail in [NIP-XX proposal](https://github.com/dostr-eth/nips/blob/ethkeygen/xx.md) and can be visually understood as follows:

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/nip-xx.png)

## How is Dostr helpful?

Dostr allows Nostr to work with Ethereum-based wallets and leverage the rich UX of Ethereum ecosystem. Dostr replaces the current web2 centralised dependencies in Nostr ecosystem (DNS and server storage) with web3 decentralised dependencies, e.g. ENS and IPFS respectively. In course of this, Dostr has unwittingly solved the problem of secure login into Nostr network from mobile devices. No universal Nostr mobile extensions existed until Dostr (Damus recently launched a Nostr client but for iOS only). In addition, most Ethereum-wallets implement ENS by default and Dostr is the first and only protocol that allows users to utilise their [NIP-02](https://github.com/nostr-protocol/nips/blob/master/02.md) compatible ENS names on Nostr. Dostr further leverages properties of ENS such as its DNS resolution via a gateway (https://vitalik.eth.LIMO) to store [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible public user information on decentralised IPFS/IPNS storage.

## Is Dostr secure?

The **Nostr-specific** private keys derived by Dostr (using the HMAC function) are **new** keys which have no invertible connection to the Ethereum wallet that derived it. Users can import or export their new generated Nostr-specific private keys in other Nostr clients and continue to use the same ENS features without exposing their Ethereum wallet private keys.

## Current status

Dostr client UI is ready and the prototype test build can be accessed on [GitHub Pages](https://dostr-eth.github.io/homepage/). Dostr client is a fork of [Astral](https://astral.ninja/) (which is a fork of Branle), and it can already be used with generic Nostr keys. Developers are currently testing [SIWE interfacing](https://github.com/dostr-eth/nips/blob/ethkeygen/xx.md) with the UI while NIP-XX is under active review. The expected release date for the client is March 31 2023.
