<p align="center"> 
<img src="https://raw.githubusercontent.com/twinecoin/twinelogo/master/reference/twinelogo256.png">
</p>

# Twinecoin Wiki

Twinecoin is a coded from scratch cryptocurrency with future scalability and extensibility in mind.

The design strategy for the coin is to release with the minimal features required for a function to operate but in a way that supports future expansion.

The target for the first release is for software that can handle mining and person to person payments.

## Features

* Separate consensus library written in c to allow for competing clients that are compatible
* The helix chain system eliminates the advantage provided by high bandwidth for miners
* 1 minute block time (8 minutes for all 8 chains to update)
* SPV nodes don’t need to download all headers
* Blocks can be validated without data from other blocks
* Blocks processing in chunks
* Extensible design – formal soft fork system
* Compatible with Bitcoin mining hardware
