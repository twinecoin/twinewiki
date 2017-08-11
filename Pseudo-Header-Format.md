# Pseudo-Header

The Twinecoin block header maintains compatibility with Bitcoin mining hardware by use of a pseudo-header.

The Bitcoin header is described [Bitcoin-Header](here).

## Pseudo-Header

The pseudo-header is defined to have the following format.

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
        <th>merkle[28:31]</th>
        <th>55555555</th>
        <th>AAAAAAAA</th>
        <th>55555555</th>
    </tr>
    <tr>
        <th>16</th>
        <th>AAAAAAAA</th>
        <th>55555555</th>
        <th>AAAAAAAA</th>
        <th>55555555</th>
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
        <th>nonce[4]|merkle[1:3]</th>
        <th>merkle[4:7]</th>
        <th>nonce[0:3]</th>
    </tr>
</table>

The variable fields in the pseudo-header are gives in the following table.

|Field|Comment|
|-|-|
|merkle|Computed from virtual coinbase transaction|
|nonce|5 bytes that can be set to anything|The LSB of the timestamp field is included to allow time rolling|

## Pseudo-Coinbase

The pseudo-coinbase is a valid coinbase that takes the hash of the Twinecoin header as its input.  It is defined as follows.

<table>
    <tr>
        <th style="min-width:100px">Offset</th>
        <th style="min-width:100px">0</th>
        <th style="min-width:100px">4</th>
        <th style="min-width:100px">8</th>
        <th style="min-width:100px">12</th>
    <tr>
        <th>0</th>
        <th>01000000</th>
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
        <th>FF|23|twine_hash[0:1]</th>
        <th>twine_hash[2:5]</th>
    </tr>
    <tr>
        <th>48</th>
        <th colspan="4">twine_hash[6:21]</th>
    </tr>
    <tr>
        <th>64</th>
        <th colspan="2">twine_hash[22:29]</th>
        <th>twine_hash[30:31]|nonce2[0:1]</th>
        <th>nonce[2]|000000</th>
    </tr>
    <tr>
        <th>80</th>
        <th>00|01|0000</th>
        <th>00000000</th>
        <th>0000|016a</th>
        <th>00000000</th>
    </tr>
</table>

The merkle root for the virtual header should be calculated using this coinbase.  

The coinbase spends zero to OP_RETURN.  The scriptSig contains the twine_hash and 3 bytes for an additional nonce.

The length of the coinbase is 96 bytes which will fit in 2 rounds of SHA256.

