Creating keys and addresses
===============================================================================

In the Shelley era of Cardano, every stakeholder can have two sets of keys and 
addresses:

* Payment Keys and addresses
* Stake Keys and addresses

Cardano CLI is the best tool for create and manage them. In this tutorail I show
you how to use docker image for that.

Payment key pair
-------------------------------------------------------------------------------

A payment key is used to send and receive transactions. To generate a payment 
key pair:

.. code-block::bash
  docker run --interactive \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
    address key-gen \
      --verification-key-file payment.vkey \
      --signing-key-file payment.skey

This creates two files payment.vkey (the public verification key) and payment.skey 
(the private signing key).


Stake key pair
-------------------------------------------------------------------------------

Stake key is used to control protocol participation, create a stake pool, delegate 
and receive rewards. To generate a stake key pair:

.. code-block::bash
  docker run --interactive \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
    stake-address key-gen \
      --verification-key-file stake.vkey \
      --signing-key-file stake.skey

Stake keys are generated with stake-address command and differs from payment address
in many cases.



Payment address
-------------------------------------------------------------------------------
Both verification keys (payment.vkey and stake.vkey) are used to build the address 
and the resulting payment address is associated with these keys.

.. code-block::bash
  docker run --interactive \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
    address build \
      --payment-verification-key-file payment.vkey \
      --stake-verification-key-file stake.vkey \
      --out-file payment.addr \
      --mainnet

Note: actually, the stake key must sign an address for payment.

Stake address
-------------------------------------------------------------------------------
To generate a stake address:

.. code-block:: bash
  docker run --interactive \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
    stake-address build \
      --stake-verification-key-file stake.vkey \
      --out-file stake.addr \
      --mainnet

This address CAN'T receive payments but will receive the rewards from participating 
in the protocol.

Query the balance of an address
-------------------------------------------------------------------------------
To query the balance of an address we need a running node and the environment 
variable CARDANO_NODE_SOCKET_PATH set to the path of the node.socket. In the docker
environment, it is a little a bit difficult because CLI and the node are running in
different containers. We have to use a volume and share it with these contaienrs.

.. code-block::bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
    query utxo \
      --address $(cat payment.addr) \
      --mainnet

The result is:

.. code-block::bash
                             TxHash                                 TxIx        Amount
  --------------------------------------------------------------------------------------

NOTE: Ensure that your node has synced to the current block height which can be 
checked at explorer.cardano.org. If it is not, you may see an error referring 
to the Byron Era.

