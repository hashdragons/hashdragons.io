# <div align="center">&#x136c;</div><br/><div align="center">Hashdragons.io</div>
## <div align="center">Breed &#xb7; Trade &#xb7; Fight</div>
#### <div align="center">April 10, 2021</div>

# Hashdragons Protocol - DRAFT

<!--
## Genesis and Dragonroot
The __genesis__ transaction for Hashdragons was:
[34c16ec249253d792e246ae853af1ed93ec83551cc71c4ff017f45beb1d8b3f0](https://explorer.bitcoin.com/bch/tx/34c16ec249253d792e246ae853af1ed93ec83551cc71c4ff017f45beb1d8b3f0)

Genesis funded __dragonroot__:
[75782272cfde665d82f83d0bb54627b6b5d83e172fe34c06c170fcc95be75437](https://explorer.bitcoin.com/bch/tx/75782272cfde665d82f83d0bb54627b6b5d83e172fe34c06c170fcc95be75437)
-->
## Protocol Identifier
Hashdragons protocol has the LOKAD ID of `0x00d401d1`. By definition, the LOKAD ID is encoded little-endian, resulting in a 4 byte protocol identifier for hashdragons of `0xd101d400`

## Events
Hashdragon "transactions" are called __events__, and are contained in the `OP_RETURN` of a Bitcoin Cash (BCH) transaction. It is recommended that the `OP_RETURN` for a hashdragon event should be at output index 0 of the transaction.

The general form of a hashdragon event is:

|v<sub>out</sub>|ScriptPubKey ("Address")|BCH|Implied asset|
|-|-|-|-|
0|OP_RETURN<br/>&lt;lokad id: '0xd101d400'&gt; (4 bytes)<br/>&lt;event_type&gt; (1 byte)<br/>&lt;input_index&gt; (uint32)<br/>&lt;output_index: 'X'&gt; (uint32)<br/>&lt;cost in satoshi&gt; (uint64)<br/>&lt;payload&gt; (varies)<br/>|0|The input_index must hold a Hashdragon<br/> asset from a prior transaction for the <br/>new transaction to be considered<br/> valid according to the game rules.
...|...|any|none
X|Next Keeper|any|The Hashdragon
...|...|any|none

...where all types are in __network byte order__ (i.e. Big-Endian).

## Lifelines and the Dragonset
 A hashdragon's __lifeline__ is its linked series of valid __events__ (cf. life event) that can be traced back to dragonroot. As a result, a hashdragon may only "do" one thing at a time, and its current activity is represented by its latest valid event.

The __dragonset__ represents the current global state of the game (cf. UTXO), and is the set of Key/Value pairs for each hashdragon and the transaction hash reference containing its latest valid event in `OP_RETURN`.

A __state commitment__ can be constructed by finding the (SHA256D) Merkle Root of the hashes resulting from creating a sorted list (cf. CTOR) of `SHA256D([ hashdragon | event_txref])`, where `|` is concatenation, from the dragonset.

The __keeper__ of a hashdragon is the transaction output indicated in the event's `output_index`. If the output indicated uses P2PKH for its scriptPubKey, then the address of the keeper can be determined.

## Event Types

### Seeder
This event type was used exclusively for __seeding__. Further use after April 2020 is considered invalid.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0x00'> (1 byte)
	<reserved> (8 bytes)
	<payload> [ input_index(uint32) | output_index(uint32) | dragonseed (32 bytes) ]
```

### Dragonseed
Makes the dragonseed available for purchase at the satoshi value in `<cost>`.
To be valid the `<dragonseed>` payload __must__ match the `dragonseed` value in the prior seeder transaction.
```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD0'> (1 byte)
	<input_index> (uint32)
	<output_index> (uint32)
	<cost: (in satoshi)> (uint64)
	<dragonseed> (32 bytes)
```

### Hatch
Instantiates the hashdragon from its dragonseed, where to be valid the SHA256D hash of the `<hashdragon>` payload __must__ match the `<dragonseed>` payload in the prior transaction.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD1'> (1 byte)
	<input_index> (uint32)
	<output_index> (uint32)
	<cost: '0'> (uint32)
	<hashdragon>  (32 bytes)
```


### Wander
Wandering allows the hashdragon to be __moved__ to another keeper address, and so potentially be __gifted__ to a new keeper. It may also indicate that a hashdragon is waking from hibernation (see below), but not participating in any specific events right now.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD2'> (1 byte)
	<input_index> (uint32)
	<output_index> (uint32)
```
### Rescue
The 'wander' event also allows the hashdragon to be __rescued__ where the keeper's output was spent with either no `OP_RETURN`, or had an invalid hashdragon event in its `OP_RETURN`. Rescue is achieved by adding the transaction hash for the last valid event for that hashdragon as the payload.

The referenced input _must_ contain the keeper address in the __scriptSig__, and so the transaction will be signature-checked to ensure the keeper has not changed.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD2'> (1 byte)
	<input_index> (uint32)
	<output_index> (uint32)
	<transaction_ref> (32 bytes)
```
To avoid the creation of `doppelgangers` or duplicated lifelines for a hashdragon, for example by issuing another valid event and a rescue event in a single block, or multiple rescues in a single block, the following additional validity restrictions apply to rescues:

- Where a valid event and a rescue for the same hashdragon appear in the same block, _the rescue is invalid_, and must not be used to update the dragonset.
- Where multiple rescues for the same hashdragon appear in the same block the rescue used to update the dragonset will be the rescue that has the __lowest__ transaction hash value after being exclusive or'd with the hash of the block header in which duplicates appear, i.e.:

`IF <rescue_tx_ref1 XOR block hash> < <rescue_tx_ref2 XOR block_hash> => rescue_tx_ref1`




### Hibernate
Hibernating allows the keeper to declare the hashdragon "asleep" and unlikely to participate in any activity for a while.
```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD3'> (1 byte)
	<input_index> (uint32)
	<output_index> (uint32)
```

### Breed
The breeding algorithm is defined to be:
```
<hashdragon1> XOR <hashdragon2> XOR <block_hash> = [0x00 | <virtues(31)>]
	=> [0xD4 | <virtues(31)>] = hashdragon3
```
...where `<block_hash>` is the hash of the block header _in which the breed event occurs_, consequently the final result can only be partially predicted.

IMPORTANT: This event is only valid if the Maturity value for both dragons equals or exceeds the block height offset of this event since their original Hatch events.  Maturity is bytes 28-29 of the hashdragon (a two byte big-endian integer). In theory, this could be anything from the block after their hatching, or up to, at worst, 15 months.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0xD4'> (1 byte)
	<first_input_index> (uint32)
	<first_output_index> (uint32)
	<second_input_index> (uint32)
	<second_output_index> (uint32)
	<third_output_index> (uint32)
```
---
### Trade
TBA
### Fight
TBA

...more...

(__scriptSig__, __scriptPubKey__)
