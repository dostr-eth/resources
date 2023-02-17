![Banner](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/banner.png)
# Dostr Technical Specification
![Icon](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/icon.png)
#### Authors:`sshmatrix` & `0xc0de4c0ffee`
#### Links: [[GitHub](https://github.com/dostr-eth)] [[.ETH](https://dostr.eth.limo)] [[.XYZ](https://dostr.xyz)]
[[Goerli]()]
###### tags: `specification` `design` `architecture` `nostr` `dostr`
# DOSTR 🔘

~~DO Signed-Tx-over Relay~~


### tldr;
> Dostr is a Nostr client with Ethereum and ENS integration. Dostr is designed as an alternative to Whisper (shh)
messaging on top of Nostr protocol.



## Introduction :

Dostr is fully backwards compatible Nostr client with ENS and Ethereum integration. It's a collection of *experimental*
specifications and implementations of Ethereum-aware Nostr clients to transfer signed data/tx between users and
services.


## Background
Ethereum was originally designed as 3 in 1 protocol. Ethereum/EVM `consensus` mechanism, Swarm `storage` system and Whisper  `messaging` protocol. 
![Icon](https://raw.githubusercontent.com/0xc0de4c0ffee/resources/whisper/graphics/web3.png)


- NOSTR : Notes and Other Stuff Transmitted by Relays
- DOSTR : DO Signed Transaction-with Relays

## Abstract

~~Dostr is a collection of specifications and implementations of Ethereum-aware
[Nostr](https://github.com/nostr-protocol/nostr#readme) client. Dostr is fully backwards compatible with Nostr
specifications, .. Dostr is using Nostr protocol as as alternative to Whisper (shh). ..relay backed peer-to-peer
messaging capabilities to the Ethereum network.~~

## Terminology
### a) `username`
Dostr username can be either of the following:

`username` or `user@domain.eth.limo` or `domain.eth.limo` or `sub.domain.eth.limo`, where,

- `username` is a [NIP-02](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible name,
- `name@domain.eth.limo` is a [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible name,
- `domain.eth.limo` is [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) equivalent of
`_@domain.eth.limo`,
- `sub.domain.eth.limo` is [NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) equivalent of
`_@sub.domain.eth.limo`.
>> Note : `sub@domain.eth.limo` and `sub.domain.eth.limo` are NOT same ID as their signatures will be different. 


### b) `password`
Password is optional string value used in HKDF salt,
```js
let username = "me@sshmatrix.eth.limo"
let password = "horse staple battery"
let signature = wallet.signMessage(message);
let salt = await sha256(`eip155:${chainId}:${username}:${password?password:""}:${signature.slice(68)}`);
```

### c) `message`
```js
let message = `Login to Nostr as ${username}\n\nWARNING : DO NOT SIGN THIS REQUEST FROM UNTRUSTED NOSTR
CLIENTS.\neip155:${chainId}:${username}:${address}`
```
### d) `signature`
Deterministic signature from connected wallet.
```js
let signature = wallet.signMessage(message); 
```
### e) `HKDF` 
- Input key is sha256 of signature bytes.
   ```js
   let inputKey = await sha256(hexToBytes(signature.slice(2)));
   ```
- Salt is sha256 hash of following string. 
   ```js
   let salt = await sha256(`eip155:${chainId}:${username}:${password?password:""}:${signature.slice(68)}`);
   ```
- Info is string with following format.
   ```js
   let info = `eip155:${chainId}:${username}:${address}`;
   ```
- Derived Key Length is set to 42. FIPS 186/4 B.4.1 require hash length to be >=n+8 where, n is length of final private key. (42 >= 32 + 8)
   ```js
   let dkLen = 42; 
   ```
- Hash to Private key is FIPs 186/4 B.4.1 implementation to convert derived hash key to valid `secp256k1` private key. this function is implemented in `@noble/secp256k1`
   ```js
   let hashKey = hkdf(sha256, inputKey, salt, info, dkLen);
   let privKey = secp256k1.hashToPrivateKey(hashKey);
   let pubKey = secp256k1.getPublicKey(privKey);
   ```
~~~
## Draft : scratchpad

`Notes and Other Stuff Transmitted by Relays`, Nostr is a open peer-to-peer networking protocol with following
properties:

- Nostr consists of **Clients** and multiple **Relays**. Clients are Nostr compatible app/websites and relays are
websocket servers connected to multiple clients (and posibbly other relays).
- Nostr is NOT peer to peer protocol,
- Nostr clients have the ability to talk to multiple relays and make requests for filtered information, which the relay
received from another client and returns to the original requester.
- Nostr users are identified by their private keys and may have a NIP02 (link) `petname` and NIP05 ID
`name@example.com`. Nostr clients sign all data they send out to relays, and relays validate that data after receiving
it. Nostr clients can also verifiy all data received from relays.

~~Dostr replaces the Bitcoin-native [Schnorr signature](https://bips.xyz/340) scheme of
[Nostr](https://github.com/nostr-protocol/nips/blob/master/01.md) standard with Ethereum-native ECDSA signature scheme.
This allows Nostr to function with Ethereum wallets and leverage properties of [ENS (Ethereum Name
Service)](https://docs.ens.domains/) protocol in Dostr client infrastructure. Consequently, Dostr provides an optional
side-stack of Ethereum-based services for Nostr users, e.g. allows them to use their ENS identity on Nostr or attach to
[Helix2](https://github.com/helix-coupler/resources/blob/master/yellow-paper/README.md) as a submodule hook.~~~

More details about the Nostr protocol may be found
[here](https://github.com/rajarshimaitra/rust-nostr/blob/main/VISION.md) (and
[here](https://github.com/nostr-protocol/nips)). In native Nostr implementation, Schnorr signature scheme over
`secp256k1` is used as per Bitcoin-native [BIP-340
standard](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki#design). Dostr uses
[RFC6979](https://datatracker.ietf.org/doc/rfc6979/) and [EIP-191](https://eips.ethereum.org/EIPS/eip-191) to generate
deterministic private key generation process to generate `secp256k1` key pairs..

~~implementing the Ethereum-based ECDSA scheme derived from and [EIP-712](https://eips.ethereum.org/EIPS/eip-712). In
Dostr clients, ENS names of the signing wallets become the petnames of the users.~~

Implementing an Ethereum-based side-stack to Nostr has several utilities. Firstly, it allows Nostr to work with
Ethereum-based wallets and leverage the rich UX of Ethereum ecosystem. For instance, most Ethereum-wallets implement ENS
by default and Dostr is the first and only protocol that allows users to uitlise their
[NIP-02](https://github.com/nostr-protocol/nips/blob/master/02.md) compatible ENS names on Nostr. Dostr further
leverages properties of ENS such as its DNS resolution via a gateway (https://vitalik.eth.limo) to store
[NIP-05](https://github.com/nostr-protocol/nips/blob/master/05.md) compatible user informations. The cross-chain utility
of Dostr is not limited to ENS and extends beyond to other Ethereum-native naming and linking protocols such as
(https://github.com/helix-coupler/resources/blob/master/yellow-paper/README.md) or [Woolball](https://woolball.xyz).

## Protocol Design

Dostr design is identitical to the Nostr specifications described in
[NIP-01](https://github.com/nostr-protocol/nips/blob/master/01.md). ~~~except for two main differences - ECDSA signature
scheme, and use of ENS via a gateway to access the user properties instead of a web2 DNS server. The detailed NIP-69
proposal covering these two aspects can be found [here](https://github.com/dostr-eth/nips/blob/ethkeygen/xx.md). In
summary, the Dostr implementation can be summarised with the following pseudo-code.~~~

## Stack & Features
### A) Deterministic Private-key derivation from signature
shh_newIdentity (deprecated)
Creates new whisper identity in the client.

Dostr uses [HMAC-based Extract-and-Expand Key Derivation Function (HKDF)](https://datatracker.ietf.org/doc/html/rfc5869)
coupled with `sha256`.

```js
import { hkdf } from '@noble/hashes/hkdf';
import { sha256 } from '@noble/hashes/sha256';
import * as secp256k1 from '@noble/secp256k1';

let username = 'petname@domain.eth.limo'; // chosen username as per NIP-02 and/or NIP-05
let optPassword = "horse staple battery"; // optional password
let address = wallet.getAddress(); // get checksum'd address from eth wallet
let signature = wallet.signMessage(message); // request signature from eth wallet (v, r, s)
let inputKey = sha256(signature);
let salt = sha256(`nostr:${username}:${optPassword}:${signature.slice(68)}`); // generate salt with username, password &
signature
let info = `nostr:${username}:${address}`; // HKDF() arg
let dkLen = 42; // HKDF() arg
let hashKey = hkdf(sha256, inputKey, salt, info, dkLen); // calculate keyhash with HKDF function
let privKey = nobleSecp256k1.hashToPrivateKey(hashKey); // private key from (keyhash ⊕ secp256k1)
let pubKey = nobleSecp256k1.getPublicKey(privKey); // public key from (keyhash ⊕ secp256k1)
```

####

## Contracts

--

## Discussion

--