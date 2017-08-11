# Note on ASICBoost

The ASICBoost optimization works by generating multiple chunk 1 headers that match the same chunk 2 header.  By including 11 bytes of the merkle root in chunk 2, it greatly reduces the chances of finding a match.

Each coinbase that is tested generates a psuedo-random value for the 11 merkle bytes in chunk 2.  The odds of two coinbases having a match is one in 2<sup>88</sup> attempts.

Collision finding is O(sqrt(N)).  Finding a collision would take on the order of 2<sup>44</sup> hashes.  Low memory algorithms are slower, but may be easier to design ASICs for.

At the time of writing, the Bitcoin network has a hash rate of around 3 million TH/s.  This is 2<sup>61</sup> hashes per second.

If that kind of hashing power can be thrown at the problem, then ASICBoost cannot be fully defended against.
