# D4 API Version 1 Proposal
__TO DO__: Once updated and finalized, we need to convert this to an OpenAPI 3.0 JSON schema file to allow code generation by potential implementors.

This API should have no requirement to hold private keys or other secrets within the implementing system.

## System API
Information about the host of this API instance and the current state of the API server.
```bash
GET /d4/api/v1/system/status

Response [application/json]:
{
	"protocol": "#d101d400",
	"utc": 1622220663,
	"uptime": "2021-04-04T18:23:30Z",
	"relay": 1000,
	"height": 630000,
	"dragonset": {
		"commitment": "ce97bf5678ef44e80c07ee1c084e99dc41fadc3a0881ba4b04769b0ad1fc0bc1",
		"state": "1cca3622b2ab64616939f78f6d5ad3268aaf1e813abed25f2abbb95634b44316"
	},
	"email": "api@solarbit.cc",
	"donate": "bitcoincash:qr3pdeun2z0c55rfzmtjhrwmqvw00fr7tct8rj0hc3",
	"message": "Run by volunteers at solarbit.cc"
}
```

## Hashdragon API
Methods to analyse a Hashdragon according to the rules.
### General Information
```bash
GET /d4/api/v1/hashdragon/info/{hex}

Response [application/json] ->
{
	"hashdragon": "d481b87bfa7a458e30c8ac12e99570753d05fb96d1320284d320d0616623787c",
	"provenance": {
		"type": "unborn" | "original" | "spawn",
		"origin": "2021-05-20T18:23:30Z"
	},
	"activity": {
		"event": "hatch" | "wander" | "hibernate" | "trapped" | "rescue" | "breed" | "trade" | "fight",
		"timestamp": "2021-05-20T18:23:30Z",
		"keeper": "address"
	},
	"location": {
		"block": 6300001,
		"tx": "94e24dfe8d9619f8aa61fc947655f5a166a7027cc74fc96e2c3b13e12a214b51"
		"op_return": []
	}
}
```

### Description
Parse out the virtue values and other attributes.
```bash
GET /d4/api/v1/hashdragon/describe/{hex}

Response [application/json] ->
{
	"hashdragon": "d481b87bfa7a458e30c8ac12e99570753d05fb96d1320284d320d0616623787c",
	"description": "Strange, Dishonest and Magical",
	"strength": 118,
	"identity": [1,0,0,0,0,0,0,1,1,0,1,1,1,0,0,0],
	"inner_light": 123,
	"color": "#fa7a45",
	"colorname": "Coral",
	"presence": 142,
	"strangeness": 200,
	"charm": 48,
	"beauty": 172,
	"truth": 18
	"magic": 223,
	"powers": [1,0,0,1,0,1,0,1,0,1,1,1,0,0,0,0,0,1,1,1,0,1,0,1],
	"manifestation": 1023802262,
	"arcana": 3509715588,
	"cabala": 3542143073,
	"maturity": "2021-05-28T22:08:29Z",
	"sigil": [120, 104],
	"next_premonition": "2022-05-20T18:23:30Z"
}
```
### Address
Generate a custom address using BCH address encoding.
```bash
GET /d4/api/v1/hashdragon/address/{hex}

Response [application/json]:
"hashdragon:d02wn576esu7ejt6getuvytp2zyky9rgxyjc3xwu5mvqw3m4mp06za48wfd3u"
```

### QR Code
Create a QR code image for the Hashdragon in PNG format.
```bash
GET /d4/api/v1/hashdragon/qr/{hex}

Response [image/png]:
<octet-string>
```
### Song
Optional. Generate a midi file from the Hashdragon.
```bash
GET /d4/api/v1/hashdragon/song/{hex}?style=[default|chant|strings]

Response [audio/midi]:
<octet-string>
```

### Lifeline
Trace the history of the Hashdragon.
```bash
GET /d4/api/v1/hashdragon/lifeline/{hex}

Response [application/json]:
[
	{
		"timestamp": "2021-04-28T22:08:29Z",
		"event": "hatch",
		"txid": "94e24dfe8d9619f8aa61fc947655f5a166a7027cc74fc96e2c3b13e12a214b51"
	},
	{
		"timestamp": "2021-05-02T22:08:29Z",
		"event": "wander",
		"txid": "94e24dfe8d9619f8aa61fc947655f5a166a7027cc74fc96e2c3b13e12a214b51"
	},
	{
		"timestamp": "2021-05-21T22:08:29Z",
		"event": "hibernate",
		"txid": "94e24dfe8d9619f8aa61fc947655f5a166a7027cc74fc96e2c3b13e12a214b51"
	},
	...
]
```

## Event API
Methods used to create transactions that act on a Hashdragon.

### Create
Provide the BCH transaction data required to generate the "hashes to sign"
```bash
POST /d4/api/v1/event/create
[application/json]
{
	"event": "wander" | "hibernate" | "rescue" | "breed" | "trade" | "fight",
	"inputs": [
		{
			"tx": "94e2...",
			"index": 1,
		},
		{
			"tx": "66a7...",
			"index": 3,
		},
		{
			"tx": "9476...",
			"index": 0,
		},
		...
	]
	"outputs": [
		{
			"target": "keeper",
			"address": "qr3pdeun2z0c55rfzmtjhrwmqvw00fr7tct8rj0hc3",
			"value": 10000
		},
		{
			"target": "change",
			"address": "qr3pdeun2z0c55rfzmtjhrwmqvw00fr7tct8rj0hc3",
			"value": 3240000
		},
		{
			"target": "other",
			"address": "qr3pdeun2z0c55rfzmtjhrwmqvw00fr7tct8rj0hc3",
			"value": 240000
		},
		...
	]
}
Response [application/json]:
{
	"raw": "66a7027cc...",
	"sign": [
		{
			"hash": "4fc9...",
			"address": "qp3pmen..."
		},
		{
			"hash": "4fc9...",
			"address": "qp3pmen..."
		},
		{
			"hash": "4fc9...",
			"address": "qp3pmen..."
		},
		...
	]
}
```
### Send
Return the appropriate signatures for the above call to generate a valid transaction and send it to the BCH blockchain.
```bash
POST /d4/api/v1/event/send
[application/json]
{
	"raw": "66a7027cc...",
	"sig_script": [
		{
			"public_key": "03750103bc0f1....",
			"signature": "304402202645e2..."
		},
		{
			"public_key": "03750103bc0f1....",
			"signature": "304402202645e2..."
		},
		{
			"public_key": "03750103bc0f1....",
			"signature": "3044022073cf201..."
		}
	]
}
Response [application/json]
{
	"tx": "7cc7...",
	"raw": "0100000009d2629b40891bde16df8bb1f8..."
}
