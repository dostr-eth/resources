# Receive BTC over ⚡ Lightning Network to .eth LUD-06 identifier via [RFC 8615](https://www.rfc-editor.org/rfc/rfc8615) `.well-known` standard

#### author(s): `0xc0de4c0ffee`, `sshmatrix`

**Disclaimer:** Only tested with [ln.tips](https://ln.tips) Lightning wallet

## Pre-requisites:

**a)** Your [ENS domain](https://ens.domains) `mydomain.eth` accessible via a public gateway, e.g. `.limo`. Your LUD-06 identifier then reads like `username@mydomain.eth.limo`

**b)** A Lightning wallet to receive BTC via `mydomain.eth.limo`

- **Pro Tip 1:** Try [ln.tips](https://ln.tips) by pinging [`@LightningTipBot`](https://telegram.im/@lightningtipbot) on Telegram. Your Lightning wallet name should then be `username@ln.tips` on Telegram. This is where you will receive your BTC sent over Lightning network to `mydomain.eth.limo`
- **Pro Tip 2:** Typical wallet name format used by Lightning providers is `username@lightning.wallet`

**c)** Another Lightning wallet to send BTC to `mydomain.eth.limo`

- **Pro Tip 3:** Make two accounts on [ln.tips](https://ln.tips) on Telegram, or use [coinos.io](https://coinos.io)
- **Pro Tip 4:** You can populate your Lightning wallets from [Kraken](https://www.kraken.com/), [Bitfinex](https://www.bitfinex.com/)

## Set Records on ENS :

Once you have the prerequisites ready, follow these steps:

**d)** Create a directory `myfolder` anywhere and the following folders inside it: `myfolder/.well-known/lnurlp/`

**e)** Create a file named `username` (without any extension) inside the newly created folder `myfolder/.well-known/lnurlp/`, and populate it with content using the following template:

```
{
  "status":"OK",
  "callback":"https://lightning.wallet/.well-known/lnurlp/username",
  "tag":"payRequest",
  "maxSendable":45000000,
  "minSendable":1000,
  "commentAllowed":150,
  "metadata":"[[\"text/plain\", \"Pay to username@mydomain.eth\"], [\"text/identifier\", \"username@lightning.wallet\"]]"
}
```

Please replace the value of `username`, `mydomain.eth` and `lightning.wallet` appropriately.

**f)** Upload folder `myfolder/` to IPFS and host your `mydomain.eth` content over IPNS instead of IPFS. This is critical in implementing [RFC 8615](https://www.rfc-editor.org/rfc/rfc8615) standard ([example](https://raw.githubusercontent.com/dostr-eth/resources/main/extra/flow.png))

- **Pro Tip 5:** Use [dWebServices](https://dwebservices.xyz) to pin your IPNS key for free. Populate your IPNS key with the IPFS hash of hosted directory `myfolder/` ([example](https://raw.githubusercontent.com/dostr-eth/resources/main/extra/dWebServices.png))
- **Pro Tip 6:** Use [Pinata](https://pinata.cloud) to host your directory for free and get its IPFS hash ([example](https://raw.githubusercontent.com/dostr-eth/resources/main/extra/pinata.png))

🥳 Voila! This should work. You should now be able to receive ⚡ Lightning BTC with your LUD-06 identifier `username@mydomain.eth.limo` 🎉

#
### Don't like this manual work?
We expect to automate this entire procedure on Dostr app very soon! 🤞
