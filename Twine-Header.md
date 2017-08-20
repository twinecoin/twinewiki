# Twinecoin Header

The Twinecoin header consists of multiple parts.  

The POW header is sent first via UDP and the block header is sent later along with the block data.  The inferred header does not need to be sent, since it can be inferred from the POW header.

**Note**: Big-Endian encoding is used for all values.

## POW Header

When a new block is found, the POW header is sent via the UDP messaging system.  This allows miners to switch to mining the next block immediately.
    
     uint64 nonce
     uint32 timestamp (seconds divided by 256)
     uint32 target
     uint8 target_shift
     hash256 previous
     hash256 uncle
     uint8 subchain_offset
     TxOut mint_payout
     int16 proposed_soft_fork_id
     uint16 soft_fork_support
     hash256 DSHA256(inferred_header)
**<center>POW Header</center>**


An additional field is included in the UDP message, but it is not part of the POW header.      

     hash256 DSHA256(block_header)

## Inferred POW Header

The inferred header contains data that can be determined from information about the previous block.

The block header hash field is given in the POW header network message, since it is an exception.

A header is defined as valid once the POW header is received and validated.  If the miner withholds the block header, the block is can be skipped, but the miner still gets minting fees.

     uint32 height
     hash256 previous_4096
     hash256 previous_256
     hash256 previous_subchain_main_child
     uint32 median_time_past
     h256 total_work
     uint32[] previous_10_timestamps
     uint16[] proposed_soft_fork_id
     uint16[] proposed_soft_fork_counts
     uint16[] in_progress_soft_fork_ids
     uint16[] in_progress_soft_fork_count
     uint16[] locked_soft_forks
     uint16[] active_soft_forks
     hash256 DSHA256(block_header)
**<center>Inferred Header</center>**

The inferred header is hashed in parts to keep the header chains for chain tip proofs shorter.

     uint32 height
     hash256 previous_4096
     hash256 DSHA256(part2)
**<center>Inferred Header - Part 1</center>**

     hash256 previous_256
     hash256 DSHA256(part3)
**<center>Inferred Header - Part 2</center>**

     hash256 previous_subchain
     hash256 DSHA256(part4)
**<center>Inferred Header - Part 3</center>**

     uint32 median_time_past
     h256 total_work
     hash256 DSHA256(part5)
**<center>Inferred Header - Part 4</center>**

     uint32[] previous_10_timestamps
     uint16[] proposed_soft_fork_id
     uint16[] proposed_soft_fork_counts
     uint16[] in_progress_soft_fork_ids
     uint16[] in_progress_soft_fork_count
     uint16[] locked_soft_forks
     uint16[] active_soft_forks
     hash256 DSHA256(block_header)
**<center>Inferred Header - Part 5</center>**

## Block Header

The block header is extendable.  The pages of the block header are sent along with the actual block data.  The first field in each page is an uint8 giving the size of the page.

The fields in each page and the number of pages may not be modified except by soft fork.  

Nodes which are up to date with all active soft forks must reject blocks that have the wrong number of pages and blocks with wrong sized pages.  If there are any unknown soft forks active, then the node should only reject the block if there are to few pages or if any pages are to small.

Soft forks may increase the maximum number of pages allowed and add fields to the end of existing pages.

     uint8 page_size
     uint8 page_count
     h256 miner_id
     h256 block_data (or all zeros for no block data)
     h256 DSHA256(block_header_page2) or all zeros
**<center>Page 1 Block Header</center>**

