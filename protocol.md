# <div align="center">&#x136c;</div><br/><div align="center">Hashdragons.io</div>
## <div align="center">Breed &#xb7; Trade &#xb7; Fight</div>
#### <div align="center">April 7, 2021</div>

## Hashdragons Protocol - DRAFT
The general format for a hashdragons transaction in `OP_RETURN` is (where `LE` means an integer value is encoded in little-endian format):

|v<sub>out</sub>|ScriptPubKey ("Address")|BCH|Implied asset|
|-|-|-|-|
0|OP_RETURN<br/>&lt;lokad id: '0xd101d400'&gt; (4 bytes)<br/>&lt;event_type&gt; (1 byte)<br/>&lt;input_index&gt; (4 byte LE integer)<br/>&lt;output_index: 'X'&gt; (4 byte LE integer)<br/>&lt;cost in satoshi&gt; (8 byte LE integer)<br/>&lt;payload&gt; (varies)<br/>|0|The input_index must hold a Hashdragon<br/> asset from a prior transaction for the <br/>new transaction to be considered<br/> valid according to the game rules.
...|...|any|none
X|Next keeper|any|The Hashdragon
...|...|any|none

The __keeper__ of a hashdragon is the unspent transaction address that is paired with a valid hashdragon `OP_RETURN` that can be traced back to dragonroot.
## Lifelines and the Dragonset
A hashdragon's lifeline is a series of __events__. A hashdragon may only do one thing at a time.

## Event Types
Hashdragon transactions are called __events__ (cf. life event)

### Seeder
This event type was used exclusively for __seeding__. Further use is after April 2020 is considered invalid.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0x00'> (1 byte)
	<reserved> (8 bytes)
	<payload> [ input_index(4) | output_index(4) | dragonseed (32) ]
```

### Dragonseed
Makes the dragonseed available for purchase at the satoshi value in `<cost>`.
To be valid the `<dragonseed>` payload __must__ match the `dragonseed` value in the prior seeder transaction.
```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD0'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index:> (4 byte LE integer)
	<cost: (in satoshi)> (8 byte LE integer)
	<dragonseed> (32 bytes)
```

### Hatch
Instantiates the hashdragon from its dragonseed, where to be valid the SHA256D hash of the `<hashdragon>` payload __must__ match the `<dragonseed>` payload in the prior transaction.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD1'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index> (4 byte LE integer)
	<cost: '0'> (8 byte LE integer)
	<hashdragon>  (32 bytes)
```


### Wander
Wandering allows the hashdragon to be moved to another keeper address, and so be __gifted__ to a new keeper. It may also indicate that a hashdragon is waking from hiberation (see below), but not participating in any specific events right now.

NOTE: The 'wander' transaction also allows the hashdragon to be __rescued__ where the keeper's output was spent with either no `OP_RETURN`, or had an invalid hashdragon event in its `OP_RETURN`. Rescue is achieved by adding the transaction hash for the last valid event for that hashdragon as the payload. The referenced input _must_ contain the keeper address in the __scriptSig__, and so the transaction will be signature checked to ensure the keeper has not changed.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD2'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index:> (4 byte LE integer)
	(optional) <transaction_ref> (32 bytes LE)
```


### Hibernate
Hiberating allow the keeper to declare the hashdragon "asleep" and unlikely to participate in any activity for a while.
```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_code: '0xD3'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index> (4 byte LE integer)
```

### Breed
The breeding algorithm is defined to be:
```
<hashdragon1> XOR <hashdragon2> XOR <block_hash> = [0x00 | <virtues(31)>]
	=> [0xD4 | <virtues{31}>] = hashdragon3
```
...where `<block_hash>` is the hash of the block header _in which the breed event occurs_, consequently the final result can only be partially predicted.

IMPORTANT: This event is only valid if the Maturity value for both dragons equal or exceed the block height offset of this event since their original Hatch events.  Maturity is bytes 28-29 of the hashdragon a two byte big-endian integer). In theory, this could be anything from the block after their hatching, or up to, at worst, 15 months.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0xD4'> (1 byte)
	<first_input_index> (4 byte LE integer)
	<first_output_index> (4 byte LE integer)
	<second_input_index> (4 byte LE integer)
	<second_output_index> (4 byte LE integer)
	<third_output_index> (4 byte LE integer)
```
---
### Trade
TBA
### Fight
TBA

...more...

(__scriptSig__, __scriptPubKey__)
