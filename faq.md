# Frequently Asked Questions

### What is a hashdragon?

A hashdragon is a mythical creature living on BCH.  All hashdragons were seeded prior to the Bitcoin Cash halving in April 2020, and all descend from the *dragonroot* in BCH block 629590: https://explorer.bitcoin.com/bch/tx/75782272cfde665d82f83d0bb54627b6b5d83e172fe34c06c170fcc95be75437.

A hashdragon is a 32 byte sequence beginning with the magic byte value `0xD4`, or *dragoncode*. The dragoncode is followed by 31 bytes which describe its attributes or *virtues*.

There is a strange and complex mythic backstory to the hashdragons that will be revealed as the game develops.

### How do I know who the keeper of a hashdragon is?

The keeper of a hashdragon is the output transaction paired with a valid hashdragon `OP_RETURN` that can be traced back to dragonroot.

For example, if a hashdragon has just hatched, the `OP_RETURN` clause would [look like](https://www.blockchain.com/bch/tx/4c1a03d0219b2bdaa7efadebd43f7fb64abf2800ef84a6d7f13a5b4ce53e5c82):

```
OP_RETURN
d101d400
d1
01000000
01000000
0000000000000000
d4a7bcef1819b01a9e57ecbc430d9b3b059dda38428ecddda440e5273c44c885
```

Per the [protocol](https://github.com/hashdragons/hashdragons.io/blob/master/hashdragons1.md#where-do-hashdragons-live), the second `01000000` is the index of the output designating the keeper.  The output at index 1 goes to address `qrypmrwr8d53xg26ktwt803l8aeu6yeh7y2rw0dx8k` so the owner of that address is the keeper of the hashdragon `d4a7bcef1819b01a9e57ecbc430d9b3b059dda38428ecddda440e5273c44c885`.

### Why aren't the hashdragons SLP Tokens?

Hashdragons do not use the SLP Tokens protocol, but its own protocol using `OP_RETURN`.

There are two reasons for this, both rather technical:

- Because SLP doesn't support the on-chain atomic swaps needed to execute a transfer of an asset in exchange for a payment without either a moderator/third party or p2p private communication.
- Because the validity of a hashdragon game event often relies on rules that use the block hash of the event that the transaction is in (which is not predictable).

So SLP can associate and send NFTs but can't trade them without something else going on.
