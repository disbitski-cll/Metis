# Locking Pool / NFT

Metis uses the POS contract to manage the entry and exit of the sequencer node set, which can be deployed in L1

## Entry and replacement of sequencer nodes

Anyone can mortgage metis and apply to become a sequencer. When the number of active sequencers reaches the agreed maximum, the newly applied node will enter the waiting queue.

If the sequencer is unhealthy for a long time, they will be withdrawn from the network. When there is free space in the sequencer pool, remove the top node from the head of the waiting queue and enter the pool.

Reference examples:

* [https://github.com/poanetwork/posdao-contracts](https://github.com/poanetwork/posdao-contracts)
* [https://github.com/bnb-chain/bsc-genesis-contract/tree/master/contracts](https://github.com/bnb-chain/bsc-genesis-contract/tree/master/contracts)
* [https://github.com/maticnetwork/contracts](https://github.com/maticnetwork/contracts)

## **Locking NFT**

Every user who successfully locks tokens and applies to become a sequencer will get an NFT. The tokenId of the NFT is the sequence id corresponding to the sequencer. On the contrary, when the unlocked token exits the sequencer, the corresponding NFT token will be destroyed.

If the user accidentally transfers the NFT to others, he will not be able to withdraw the locked METIS. In order to prevent this from happening, the transfer of the NFT obtained by the user is restricted in the contract. Only the LockingPool owns the mint/burn/transfer permissions. Because after the NFT contract is successfully deployed, the owner will be transferred to the LockingPool.

If the user who has already locked the METIS really needs to replace the signer, then the updateSigner method is provided in the LockingPool contract, which can help the user to replace the signer and transfer the NFT

\
