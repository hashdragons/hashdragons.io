# <div align="center">&#x136c;</div><br/><div align="center">Hashdragons.io</div>
## <div align="center">Breed &#xb7; Trade &#xb7; Fight</div>
#### <div align="center">April 7, 2021</div>

## Hashdragons Protocol
The general format for a hashdragons transaction in `OP_RETURN` is (where `LE` means an integer value is encoded in little-endian format):

|v<sub>out</sub>|ScriptPubKey ("Address")|BCH|Implied asset|
|-|-|-|-|
0|OP_RETURN<br/>&lt;lokad id: '0xd101d400'&gt; (4 bytes)<br/>&lt;event_type&gt; (1 byte)<br/>&lt;input_index&gt; (4 byte LE integer)<br/>&lt;output_index: 'X'&gt; (4 byte LE integer)<br/>&lt;cost in satoshi&gt; (8 byte LE integer)<br/>&lt;payload&gt; (varies)<br/>|0|The input_index must hold a Hashdragon<br/> asset from a prior transaction for the <br/>new transaction to be considered<br/> valid according to the game rules.
...|...|any|none
X|Next keeper|any|The Hashdragon
...|...|any|none

The __keeper__ of a hashdragon is the unspent transaction address that is paired with a valid hashdragon `OP_RETURN` that can be traced back to dragonroot.

## Transaction Types (Part One)
Hashdragon transactions are called __events__ (cf.life event)

### Seeder
This event type was used exclusively for __seeding__ and is considered invalid if it is used today from April 2020.

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0x00'> (1 byte)
	<reserved> (8 bytes)
	<payload> [ input_index(4) | output_index(4) | dragonseed (32)]
```

### Dragonseed

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0xD0'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index:> (4 byte LE integer)
	<cost: '200000'> (8 byte LE integer)
	<dragonseed> (32 bytes)
```

### Hashdragon

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0xD1'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index> (4 byte LE integer)
	<cost: '0'> (8 byte LE integer)
	<hashdragon>  (32 bytes)
```


### Wander
Wandering allows the hashdragon to be moved to another address, and __gifted__ to a new keeper.

NOTE: The 'wander' transaction also allows the hashdragon to be __rescued__ where the keeper's output was spent with either no `OP_RETURN`, or has an invalid event in the `OP_RETURN`, by adding the last valid transaction hash as the payload, the input must contain the keeper address in the 'sig_script', and so the transaction will be signature checked to ensure the owner does not transfer.
```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0xD2'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index:> (4 byte LE integer)
	<cost: '0'> (8 byte LE integer)
	(optional) <transaction_ref> (32 bytes LE)
```

### Hibernate

```
OP_RETURN
    <lokad_id: '0xd101d400'> (4 bytes)
	<event_type: '0xD3'> (1 byte)
	<input_index> (4 byte LE integer)
	<output_index:> (4 byte LE integer)
	<cost: '0'> (8 byte LE integer)
```

### Breed
