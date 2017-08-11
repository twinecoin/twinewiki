# Proof of Work Calculation
This page concisely describes the proof of work calculation for Twinecoin.

## Inputs

The proof of work test takes as its input three values.
<br>
<center>

|Field|Name|Size|Description|
|-|-|:-:|-|
|twine_hash[0:31]|Twine Header Hash|32|DSHA256 of the Twinecoin header|
|nonce[0:4]|Header Nonce|5|Arbitrary Nonce
|nonce2[0:2]|Coinbase Nonce|3|Arbitrary Nonce

__Proof of Work Inputs__
</center>

The Twinecoin header hash is simply the DSHA256 of the header.  8 bytes of nonce data are provided. 

## Pseudo Coinbase
The pseudo-coinbase transaction is built up by concatenating the prefix, header hash, coinbase nonce and the suffix.
<center>

|Field|Hex|
|-|-|
|Prefix|01000000010000000000000000000000000000000000000000000000000000000000000000FFFFFFFF23
|twine_hash[0:31]|32-byte Twine Header Hash|
|nonce2[0:2]|3-byte Coinbase Nonce|
|Suffix|00000000010000000000000000016a00000000|

__Pseudo Coinbase__
</center>
The transaction is hashed using the DSHA256 hashing function.  Since there is only one transaction in the pseudo-block.  This hash is the root of the merkle tree (merkle[0:31]).  This is required for the next stage of the calculation.

## Pseudo Block Header
The pseudo-block header is built up by concatenating the fields in this table.
<center>

|Field|Hex|
|-|-|
|Merkle MSW|merkle[28:31]
|Fake Previous|55555555AAAAAAAA55555555AAAAAAAA<br>55555555AAAAAAAA5555555500000000
|Full Merkle|merkle[0:31]|
|Header Nonce Last Byte|nonce[4]|
|Merkle Start|merkle[1:7]
|Header Nonce First Word|nonce[0:3]|

__Pseudo Block Header__
</center>
The DSHA256 of pseudo-block header is compared against the block target.

__Note__: The hash is considered the encoding of a <u>__little__</u>-endian unsigned 256-bit number.
