# Sequencer node

<figure><img src="../../.gitbook/assets/Blank diagram.png" alt="" width="375"><figcaption></figcaption></figure>

The Sequencer node includes:&#x20;

1. L2 Geth (including the OP-Node)
2. Batch submitter (Proposer)
3. Adapter module
4. MPC module (see the next section)

## **L2 Geth (including the OP-Node)**

* It is responsible for transaction sequencing and assembly of the blocks on the Metis layer.
* The processing logic of whether it is the current block sequencer is added in the `applyTransactionToTip` function, to conduct regular sequencer rotation.
* The sequencer (OP-NODE) node obtains the current sequencer position in the rotation list which corresponds to the blockheight with the help of the Adapter module (see below) from the MPC Consensus Layer. It checks whether it is the current sequencer in the rotation –  if so, constructs the batch, if not, does not construct the batch.

The main process is described in the following flowchart:

<figure><img src="../../.gitbook/assets/Sequencer transaction diagram.png" alt="" width="375"><figcaption></figcaption></figure>

## **Batch submitter (Proposer):**

* Responsible for building the batches and submitting them to Layer 1 after they get signed by multiple sequencers;
* For decentralization it utilizes MPC – multiple sequencers sign the transaction batch jointly – when the transaction batch is being formed.
* MPC service signs the batch submission with such entry parameters: `batchID`, `signHash`, and the signature result has such parameters: `batchID` – return value: signature r, s, v-values.
* Query the corresponding `signHash` according to the `batchID`: Input parameter: `batchID` – return value: `signHash`

## **Adapter module:**

* Responsible for interacting with the other external modules on the consensus layer (PoS Node).

Additional information:

The core problem of the single-sequencer model adopted by other Layer 2 networks is the the inability to perform sequencer rotation. Here’s how Metis tackles it:&#x20;

* The rotation information is stored on the L2 contract (managed by sequencers, named as `MetisSequencerSet` contract), so that all nodes can obtain the latest sequencer rotation information through L2 transactions, and the management of the sequencer list contract is controlled by the consensus layer (PoS nodes);
* After the consensus layer produces the sequencer list information (contained in each epoch), the list will be signed by MPC, and the current sequencer will initiate a transaction to update the sequencer list;
* Each approved sequencer in the sequencer list can verify the current (picked) sequencer in rotation according to the sequencer list.
* If the current sequencer fails to order transactions within the specified time, or produces wrong transactions (like initiating two transactions with identical L2 TxID), the node is considered malicious. The PoS layer will select a new sequencer (construct `ReselectSeqencer` signature transaction, containing expected TxID, version and other information, signed by the MPC service), and the new sequencer will initiate a `ReselectSeqencer` transaction on the current TxID on L2, writing the information of the new sequencer into `MetisSequencerSet` contract.&#x20;

<figure><img src="../../.gitbook/assets/Sequencer node ReselectSeq.png" alt="" width="340"><figcaption></figcaption></figure>

* In case of rotation, when a node receives a regular transaction with TXID, it puts on hold the regular transaction and executes MetisSequencerSet.sol contract transaction based on the latest version, then the PoS layer selects the new sequencer that will execute the regular transaction. This ensures that the update to the sequencer list contract is executed, and the execution result reflects the latest state.

<figure><img src="../../.gitbook/assets/Sequencer node ReselectSeq(1).png" alt="" width="375"><figcaption></figcaption></figure>

