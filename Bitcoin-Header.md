# Bitcoin Header

## Bitcoin Header Layout

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

## SHA256 Chunk Processing

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

