---
caip: 111
title: Sign-In-With-X-on-Y
description: Application-specific Private Key Generation from Deterministic SIWx Signatures
author(s): 0xc0de4c0ffee (@0xc0de4c0ffee), sshmatrix (@sshmatrix)
status: Draft
type: Standard
created: 2023-04-05
updated: 2023-04-05
---

# CAIP-111

Sign-In-With-X-on-Y: Application-specific Keypair Generation from SIWx Signatures
--
###### tags: `draft` `optional` `author:0xc0de4c0ffee` `author:sshmatrix`

## Abstract

This specification is a proof-of-concept implementation for applications and wallet providers to generate deterministic private keys from chain-agnostic CAIP-122 signatures (`Sign-In-With-X` specification). The keypairs generated using this specification are application-specific and do not expose the original signing keypair. The new private keys are derived using SHA-256 HMAC Key Derivation Function (HKDF) with username & password, CAIP-02 Blockchain ID & CAIP-10 Account ID Specification identifiers, and deterministic signatures from connected wallets as inputs.

## Introduction

CAIP-111 at its core is an account abstraction specification in which a cryptographic signature calculated by one signing algorithm and its native keypair (e.g. [Bitcoin-native Schnorr algorithm](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki)) can be used to derive a deterministic cryptographic keypair for another signing algorithm using an appropriate singular (non-invertible) key derivation function. This specification particularly describes the case where the former and latter algorithms are [Bitcoin-native Schnorr algorithm](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki) and [Ethereum-native ECDSA algorithm](https://eips.ethereum.org/EIPS/eip-191) respectively, and the one-way adaptor from ECDSA to Schnorr keypair is HMAC-based Key Derivation Function ([HKDF](https://datatracker.ietf.org/doc/html/rfc586)). It is nonetheless trivial to invert this process and derive deterministic ECDSA keypairs from Schnorr signatures [?]. It is also trivial to extend this specification to work across elliptic curves other than `secp256k1`, for example from `secp256k1` signatures to `ed25519` private keys or vice-versa.

CAIP-111 specification originated from the desire to allow [Nostr network](https://nostr.com) to function with widely popular Ethereum wallets such as Metamask and leverage the strong network effects of Ethereum ecosystem. The problem however lay in the fact that Nostr protocol uses Schnorr algorithm on `secp256k1` for signing messages/data while Ethereum and its wallets use the more standard ECDSA algorithm on `secp256k1`. The difference in two signing algorithms and respective signing keypairs is the exact technical incompatibility that this specification originally succeeded in resolving through [NIP-111](https://github.com/dostr-eth/nips/blob/ethkeygen/111.md) by enabling Sign-In With Ethereum (SIWE) on Nostr network and birthing [Dostr - an Ethereum-flavoured Nostr](https://dostr.eth.limo) client. The underlying schema however is fully capable of functioning as a chain-agnostic workflow and this draft reflects that property by using [CAIP](https://github.com/ChainAgnostic/CAIPs) (Chain-Agnostic Improvement Proposals) implementations.

## Terminology

### a) Application Identifier
`appId` is an application identifier `string`,

```js
let applicationId = 'uniswap-v3.1'
```

### b) Username
`username` is a user identifier `string`,

```js
let username = 'alice'
```

Individual applications may choose to set their own rules on usernames, i.e. accepted formats and/or disallowed characters etc.

### c) Password
`password` is an optional `string` value used to salt the key derivation function (HKDF),
```js
let password = "horse staple battery"
```

## d) Chain-agnostic Identifiers
Chain-agnostic [CAIP-02: Blockchain ID Specification](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md) and [CAIP-10: Account ID Specification](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md) schemes are used to generate blockchain and address identifiers `caip02` and `caip10` respectively,
```js
let caip02 =
      `eip155:<evm_chain_id>` ||
      `cosmos:<hub_id_name>` ||
      `bip122:<16 bytes genesis/fork hash>`;

let caip10 = `${caip02}:<checksum_address>`;
```

### e) Info
`info` is CAIP-10 and the user identifier string formatted as:
 ```js
 let info = `${caip10}:${username}`;
 ```

### f) Message
Deterministic `message` to be signed by the wallet provider,
```js
let message = `Log into ${applicationId} as '${username}'\n\nIMPORTANT: Please verify the integrity and authenticity of connected ${applicationId} client before signing this message\n\nSIGNED BY: ${info}`
```

### g) Signature
[RFC-6979](https://datatracker.ietf.org/doc/html/rfc6979) compatible (ECDSA) deterministic `signature` calculated by the wallet provider using native keypair,
```js
let signature = wallet.signMessage(message);
```
Implementations should ideally check the validity of signatures against RFC-6979 standard at the end of this step.
### h) Salt
`salt` is SHA-256 hash of the `info`, optional password and last **32 bytes** of signature string formatted as:
  ```js
  let salt = await sha256(`${info}:${password?password:""}:${signature.slice(68)}`);
  ```
  where, `signature.slice(68)` are the last 32 bytes of the deterministic ECDSA-derived Ethereum signature.

### i) Key Derivation Function (KDF)
HMAC-Based KDF `hkdf(sha256, inputKey, salt, info, dkLen = 42)` is used to derive the **42 bytes** long **hashkey** with inputs,

- `inputKey` is SHA-256 hash of signature bytes,
   ```js
   let inputKey = await sha256(hexToBytes(signature.slice(2)));
   ```

- `info` is same as defined before, i.e.
   ```js
   let info = `${caip10}:${username}`;
   ```

- `salt` is same as defined before, i.e.
   ```js
   let salt = await sha256(`${info}:${password?password:""}:${signature.slice(68)}`);
   ```

- `dkLen` (Derived Key Length) is set to `42`,
   ```js
   let dkLen = 42;
   ```
   [FIPS 186-4 B.4.1](https://csrc.nist.gov/publications/detail/fips/186/4/final) requires hashkey length to be `>= n + 8`, where `n = 32` is the **bytelength** of the final `secp256k1` private key, such that `42 >= 32 + 8`.

- `hashToPrivateKey()` function is FIPS 186-4 B.4.1 implementation to convert HKDF-derived hashkey to valid `secp256k1` keypair. This function is implemented in JavaScript library `@noble/secp256k1` as `hashToPrivateKey()`.

   ```js
   let hashKey = hkdf(sha256, inputKey, salt, info, dkLen = 42);
   let privKey = secp256k1.utils.hashToPrivateKey(hashKey);
   let pubKey = secp256k1.schnorr.getPublicKey(privKey);
   ```
   Applications may adopt a different hashkey → private key mapping at this step based on their requirement, e.g. to `ed25519` key instead of `secp256k1`.

## Architecture

The resulting architecture of CAIP-111 can be visually interpreted as follows:

![](https://raw.githubusercontent.com/dostr-eth/resources/main/graphics/caip-111.png)

## Implementation Requirements

- Input signatures **MUST** be RFC-6979 compatible.
- The `message` **MUST** be string formatted as
```
`Log into ${applicationId} client as '${username}'\n\nIMPORTANT: Please verify the integrity and authenticity of connected ${applicationId} client before signing this message\n\nSIGNED BY: ${info}`
```
- HKDF `inputKey` **MUST** be generated as the SHA-256 hash of 65 bytes long signature.
- HKDF `salt` **MUST** be generated as SHA-256 hash of string
```
${info}:${username}:${password?password:""}:${signature.slice(68)}
```
- HKDF-derived Key Length (`dkLen`) **MUST** be 42.
- HKDF `info` **MUST** be string formatted as
```
${CAIP_10}:${address}:${username}
```

## JS Example
```js
import * as secp256k1 from '@noble/secp256k1'
import {hkdf} from '@noble/hashes/hkdf'
import {sha256} from '@noble/hashes/sha256'
import {queryProfile} from './nip05'
import {getPublicKey} from './keys'
import {ProfilePointer} from './nip19'

// const wallet = connected ethereum wallet with ethers.js
let username = "me@example.com"
let chainId = wallet.getChainId(); // get ChainID from connected wallet
let address = wallet.getAddress(); // get Address from wallet
let caip10 = `eip155:${chainId}:${address}`;
let message = `Login to Nostr as ${username}\n\nImportant: Please verify the integrity and authenticity of your Nostr client before signing this message.\n${caip10}`
let signature = wallet.signMessage(message); // request Signature from wallet
let password = "horse staple battery"

/**
 *
 * @param username NIP-02/NIP-05 identifier
 * @param caip10 CAIP identifier for the blockchain account
 * @param sig Deterministic signature from X-wallet provider
 * @param password Optional password
 * @returns Deterministic private key as hex string
 */
export async function privateKeyFromX(
  username: string,
  caip10: string,
  sig: string,
  password: string | undefined
): Promise < string > {
  if (sig.length < 64)
    throw new Error("Signature too short");
  let inputKey = await sha256(secp256k1.utils.hexToBytes(sig.toLowerCase().startsWith("0x") ? sig.slice(2) : sig))
  let info = `${caip10}:${username}`
  let salt = await sha256(`${info}:${password?password:""}:${sig.slice(-64)}`)
  let hashKey = await hkdf(sha256, inputKey, salt, info, 42)
  return secp256k1.utils.bytesToHex(secp256k1.utils.hashToPrivateKey(hashKey))
}

/**
 *
 * @param username NIP-02/NIP-05 identifier
 * @param caip10 CAIP identifier for the blockchain account
 * @param sig Deterministic signature from X-wallet provider
 * @param password Optional password
 * @returns
 */
export async function signInWithX(
  username: string,
  caip10: string,
  sig: string,
  password: string | undefined
): Promise < {
  petname: string,
  profile: ProfilePointer | null,
  privkey: string
} > {
  let profile = null
  let petname = username
  if (username.includes(".")) {
    try {
      profile = await queryProfile(username)
    } catch (e) {
      console.log(e)
      throw new Error("Nostr Profile Not Found")
    }
    if(profile == null){
      throw new Error("Nostr Profile Not Found")
    }
    petname = (username.split("@").length == 2) ? username.split("@")[0] : username.split(".")[0]
  }
  let privkey = await privateKeyFromX(username, caip10, sig, password)
  let pubkey = getPublicKey(privkey)
  if (profile?.pubkey && pubkey !== profile.pubkey) {
    throw new Error("Invalid Signature/Password")
  }
  return {
    petname,
    profile,
    privkey
  }
}
```

## Implementations
1) Dostr Toolkit : [Sign-In-With-X](https://github.com/dostr-eth/nostr-tools/tree/siwx) | [Pull Request #132](https://github.com/nbd-wtf/nostr-tools/pull/132)
2) Dostr Client: [GitHub](https://github.com/dostr-eth/dostr-client) | [XYZ](https://dostr.xyz/) | [ETH](https://dostr.eth.limo/)

## Security Considerations

- Users **SHOULD** always verify the integrity and authenticity of the Nostr client before signing the message.
- Users **SHOULD** ensure that they only input their Nostr `username` and `password` in trusted and secure clients.

## References:

- [RFC-6979: Deterministic Usage of the DSA and ECDSA](https://datatracker.ietf.org/doc/html/rfc6979)
- [RFC-5869: HKDF (HMAC-based Extract-and-Expand Key Derivation Function)](https://datatracker.ietf.org/doc/html/rfc5869)
- [CAIP-02: Blockchain ID Specification](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md)
- [CAIP-10: Account ID Specification](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md)
- [CAIP-122: Sign-in-With-X)](https://github.com/ChainAgnostic/CAIPs/pull/122)
- [Digital Signature Standard (DSS), FIPS 186-4 B.4.1](https://csrc.nist.gov/publications/detail/fips/186/4/final)
- [BIP-340: Schnorr Signature Standard](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki)
- [ERC-191: Signed Data Standard](https://eips.ethereum.org/EIPS/eip-191)
- [EIP-155: Simple Replay Attack Protection](https://eips.ethereum.org/EIPS/eip-155)
- [NIP-02: Contact List and Petnames](https://github.com/nostr-protocol/nips/blob/master/02.md)
- [NIP-05: Mapping Nostr Keys to DNS-based Internet Identifiers](https://github.com/nostr-protocol/nips/blob/master/05.md)
- [ECDSA Signature Standard](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.38.8014)
- [@noble/hashes](https://github.com/paulmillr/noble-hashes)
- [@noble/secp256k1](https://github.com/paulmillr/noble-secp256k1)
