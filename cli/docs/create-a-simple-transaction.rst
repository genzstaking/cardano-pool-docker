Create a simple transaction
===============================================================================
Creating a transaction requires various steps, I will show you these steps in 
docker. Here is the list of steps:

* Get the protocol parameters
* Calculate the fee
* Define the time-to-live (TTL) for the transaction
* Build the transaction
* Sign the transaction
* Submit the transaction

Get protocol parameters
-------------------------------------------------------------------------------
Protocol parameters are used to affect the operation of the Cardano Protocol. They 
may be either updatable or non-updatable.
Get the protocol parameters and save them to protocol.json with:

.. code-block:: bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-gen2-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    gen2-pool/cardano-cli \
      query protocol-parameters \
        --mainnet \
        --out-file protocol.json
      
 
then Get the transaction hash and index of the UTXO to spend:

.. code-block:: bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-gen2-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    gen2-pool/cardano-cli \
      query utxo \
        --address $(cat payment.addr) \
        --mainnet


Draft the transaction
-------------------------------------------------------------------------------
Create a draft for the transaction and save it in tx.draft

Note that for --tx-in we use the following syntax: TxHash#TxIx where TxHash is 
the transaction hash and TxIx is the index; for --tx-out we use: TxOut+Lovelace 
where TxOut is the hex encoded address followed by the amount in Lovelace. 
For the transaction draft --tx-out, --invalid-hereafter and --fee can be set 
to zero.

.. code-block:: bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-gen2-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    gen2-pool/cardano-cli \
      transaction build-raw \
      --tx-in 4e3a6e7fdcb0d0efa17bf79c13aed2b4cb9baf37fb1aa2e39553d5bd720c5c99#4 \
      --tx-out $(cat payment2.addr)+0 \
      --tx-out $(cat payment.addr)+0 \
      --invalid-hereafter 0 \
      --fee 0 \
      --out-file tx.draft


