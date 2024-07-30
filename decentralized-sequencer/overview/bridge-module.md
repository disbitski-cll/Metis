# Bridge module

The connection between Consensus layer (PoS) and Sequencer (Metis node). It has 2 functions:&#x20;

1. Processor:
   * Sends the transactions to TSS/Themis for consensus according to Listener
   * Scans epoch and Metis blocks information
   * If the block is wrong, submits `reProposeSpan` messages to Themis
   * Scans the MPC service interface and sends the transaction batches to the consensus layer for MPC signing
2. Listener
   * Monitors the L1 Locking contract events and obtains the info about Sequencer node list (`join` / `exit` / `update`)
   * Listens to Metis block’s events to determine whether to send tasks (It means that when the listener listens to the block events of Themis or Ethereum, it determines whether it needs to notify the Processor to perform some task processing. For example, whether it needs to enter the MPC signature process)
