# Block Header Format

The Twinecoin block header maintains compatibility with Bitcoin mining hardware by use of a virtual header.

## Virtual Bitcoin Header

The standard Bitcoin header has the following layout.

| type | Field | Size |Name|
|------|-------|------|----|
|uint32|version|4|Bitcoin block version|
|hash256|prev|32|Hash of previous block|
|hash256|merkle|32|Merkle tree root hash|
|uint32|timestamp|4|Block timestamp|
|uint32|bits|4|Difficulty target|
|uint32|nonce|4|Nonce field|

This gives a total of 80 bytes.

The SHA256 hash function hashes the header in two chunks.  The first chunk is 64-bytes long and then 2nd chunk hashes the remaining 16 bytes.

### SHA256 Chunk Processing
 
The SHA256 hash function processes data in 64 byte chunks.  The following table shows the Bitcoin header split into its two chunks.  The first chunk contains 64 bytes, but the 2nd chunk only contains 16 bytes.


<table>
    <tr>
        <th style="min-width:100px">Chunk</th>
        <th style="min-width:100px">Offset</th>
        <th style="min-width:100px">0</th>
        <th style="min-width:100px">4</th>
        <th style="min-width:100px">8</th>
        <th style="min-width:100px">12</th>
    <tr>
    <th style="border-bottom: 1px solid #000; vertical-align:middle;" rowspan="4">1</th>
        <th>0</th>
        <th>version</th>
        <th colspan="3">prev[0:11]</th>
    </tr>
    <tr>
        <th>16</th>
        <th colspan="4">prev[12:27]</th>
    </tr>
    <tr>
        <th>32</th>
        <th>prev[28:31]</th>
        <th colspan="3">merkle[0:11]</th>
    </tr>
    <tr>
        <th style="border-bottom: 1px solid #000;">48</th>
        <th style="border-bottom: 1px solid #000;" colspan="4">merkle[12:27]</th>
    </tr>
    <tr>
        <th>2</th>
        <th>64</th>
        <th>merkle[28:31]</th>
        <th>timestamp</th>
        <th>bits</th>
        <th>nonce</th>
    </tr>
</table>


### New Header Format
The virtual header is defined to have the following format.

<table>
    <tr>
        <th style="min-width:100px">Chunk</th>
        <th style="min-width:100px">Offset</th>
        <th style="min-width:100px">0</th>
        <th style="min-width:100px">4</th>
        <th style="min-width:100px">8</th>
        <th style="min-width:100px">12</th>
    <tr>
    <th style="border-bottom: 1px solid #000; vertical-align:middle;" rowspan="4">1</th>
        <th>0</th>
        <th>7E00007E</th>
        <th>55555555</th>
        <th>AAAAAAAA</th>
        <th>55555555</th>
    </tr>
    <tr>
        <th>16</th>
        <th>AAAAAAAA</th>
        <th>55555555</th>
        <th>AAAAAAAA</th>
        <th>merkle[28:31]</th>
    </tr>
    <tr>
        <th>32</th>
        <th>00000000</th>
        <th colspan="3">merkle[0:11]</th>
    </tr>
    <tr>
        <th style="border-bottom: 1px solid #000;">48</th>
        <th style="border-bottom: 1px solid #000;" colspan="4">merkle[12:27]</th>
    </tr>
    <tr>
        <th>2</th>
        <th>64</th>
        <th>merkle[28:31]</th>
        <th>nonce[0]|merkle[0:1]|nonce[1]</th>
        <th>merkle[2:3]|nonce[2:3]</th>
        <th>nonce[4:7]</th>
    </tr>
</table>

The fields in the virtual header are gives in the following table.

|Field|Comment|
|-|-|
|merkle|Computed from virtual coinbase transaction|
|nonce|8 bytes that can be set to anything<br>It consumes two bytes of the timestamp and difficulty field

### Note on ASICBoost

The ASICBoost optimization works by generating multiple chunk 1 headers that match the same chunk 2 header.  By including 8 bytes of the merkle root in chunk 2, it greatly reduces the chances of finding a match.

If two distinct coinbases generate the same chunk 1 result, then the SHA256 hash function is broken.

Each coinbase that is tested will generate a psuedo-random value for the merkle[0:7] byte string.  This means that the odds of two coinbases having a match is one in 2<sup>64</sup> attempts.

## Virtual Coinbase

The virtual coinbase is a valid coinbase that takes the hash of the twine header as its input.  It is defined as follows.

<table>
    <tr>
        <th style="min-width:100px">Offset</th>
        <th style="min-width:100px">0</th>
        <th style="min-width:100px">4</th>
        <th style="min-width:100px">8</th>
        <th style="min-width:100px">12</th>
    <tr>
        <th>0</th>
        <th>10000000</th>
        <th>01|000000</th>
        <th>00000000</th>
        <th>00000000</th>
    </tr>
    <tr>
        <th>16</th>
        <th>00000000</th>
        <th>00000000</th>
        <th>00000000</th>
        <th>00000000</th>
    </tr>
    <tr>
        <th>32</th>
        <th>00000000</th>
        <th>00|FFFFFF</th>
        <th>FF|24|twine_root[0:1]</th>
        <th>twine_root[2:5]</th>
    </tr>
    <tr>
        <th>48</th>
        <th colspan="4">twine_root[6:21]</th>
    </tr>
    <tr>
        <th>64</th>
        <th colspan="2">twine[22:29]</th>
        <th>twine_root[30:31]|0000</th>
        <th>0000|0100</th>
    </tr>
    <tr>
        <th>80</th>
        <th>00000000</th>
        <th>00000000</th>
        <th>00000001</th>
        <th>6a000000</th>
    </tr>
    <tr>
        <th>96</th>
        <th>00|</th>
    </tr>
</table>

The merkle root for the virtual header should be calculated using this coinbase.  

The coinbase spends zero to OP_RETURN.  The scriptSig contains the twine_root and 4 bytes for extra nonce.