# Set NIP-05 identifier on ENS Domain via [RFC 8615](https://www.rfc-editor.org/rfc/rfc8615) `.well-known` standard

#### author(s): `0xc0de4c0ffee`, `sshmatrix`

## Pre-requisites:

**a)** Your [ENS domain](https://ens.domains) `mydomain.eth` accessible via a public gateway, e.g. `.limo`

**b)** Your Nostr public key in hex format (must not start with `npub`)

- **Pro Tip 1:** Use 'Dev Tools' under 'Settings' tab on [Dostr](https://app.dostr.eth.limo) to convert your public key from `npub` to hex format

## Set Records on ENS :

Once you have the prerequisites ready, follow these steps:

**c)** Create a directory `myfolder` anywhere and the following folders inside it: `myfolder/.well-known/`

**d)** Create a file named `nostr.json` inside the newly created folder `myfolder/.well-known/`, and populate it with [NIP-05 compatible](https://github.com/nostr-protocol/nips/blob/master/05.md) content, for example:

```
{
  "names": {
    "username": "thisismypublickey0cd6c495b282167acf761729078d975fc341b22650b07b9"
  }
}
```

Please replace the placeholder `username`  appropriately and set your own Nostr public key as its corresponding value.

**e)** Upload folder `myfolder/` to IPFS and host your `mydomain.eth` content over IPNS instead of IPFS. This is critical in implementing [RFC 8615](https://www.rfc-editor.org/rfc/rfc8615) standard ([example](https://raw.githubusercontent.com/dostr-eth/resources/main/extra/flow.png))

- **Pro Tip 2:** Use [dWebServices](https://dwebservices.xyz) to pin your IPNS key for free. Populate your IPNS key with the IPFS hash of hosted directory `myfolder/` ([example](https://raw.githubusercontent.com/dostr-eth/resources/main/extra/dWebServices.png))
- **Pro Tip 3:** Use [Pinata](https://pinata.cloud) to host your directory for free and get its IPFS hash ([example](https://raw.githubusercontent.com/dostr-eth/resources/main/extra/pinata.png))

🥳 Voila! This should work. You should now be NIP-05 verified on Nostr at `username@mydomain.eth.limo` 🎉

#
### Don't like this manual work?
We expect to automate this entire procedure on [Dostr](https://dostr.eth.limo) app very soon! 🤞
#
