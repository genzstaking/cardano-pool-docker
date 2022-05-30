Register stake address on the blockchain
===============================================================================

Stake address needs to be registered on the blockchain to be useful. Registering 
keys requires:

* Create a registration certificate.
* Submit the certificate to the blockchain with a transaction.

Note that, the registration process has fee, so transfer some to your wallet before
start.

Create a registration certificate
-------------------------------------------------------------------------------
The first step is to create a certificate.

.. code-block::bash
  docker run --interactive \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      stake-address registration-certificate \
        --stake-verification-key-file stake.vkey \
        --out-file stake.cert

Draft transaction
-------------------------------------------------------------------------------

For the transaction draft, --tx.out, --invalid-hereafter and --fee can be set to 
zero.

.. code-block::bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction build-raw \
      --tx-in b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee#1 \
      --tx-out $(cat payment.addr)+0 \
      --invalid-hereafter 0 \
      --fee 0 \
      --out-file tx.draft \
      --certificate-file stake.cert

Calculate fees
-------------------------------------------------------------------------------


.. code-block::bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction calculate-min-fee \
        --tx-body-file tx.draft \
        --tx-in-count 1 \
        --tx-out-count 1 \
        --witness-count 2 \
        --byron-witness-count 0 \
        --mainnet \
        --protocol-params-file protocol.json

The output is the transaction fee in lovelace:


.. code-block::bash
    > 171485

Registering the stake address, not only pay transaction fees, but also includes a 
_deposit_ (which you get back when deregister the key) as stated in the protocol 
parameters:

The deposit amount can be found in the `protocol.json` under `stakeAddressDeposit`, 
for example in Shelley Mainnet:

.. code-block::json
    "stakeAddressDeposit": 2000000,
    
Query the UTXO of the address that pays for the transaction and deposit:

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

    >                            TxHash                                 TxIx      Amount
    > ----------------------------------------------------------------------------------------
    > b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee     1      1000000000 lovelace

Calculate the change to send back to payment address after including the deposit
-------------------------------------------------------------------------------

.. code-block::bash
  expr 1000000000 - 171485 - 2000000
  > 997828515

Submit the certificate with a transaction:
-------------------------------------------------------------------------------
Build the transaction, this time include  --invalid-hereafter and --fee

.. code-block::bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction build-raw \
        --tx-in b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee#1 \
        --tx-out $(cat payment.addr)+997828515 \
        --invalid-hereafter 987654 \
        --fee 171485 \
        --out-file tx.raw \
        --certificate-file stake.cert

Sign it:

.. code-block::bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction sign \
        --tx-body-file tx.raw \
        --signing-key-file payment.skey \
        --signing-key-file stake.skey \
        --mainnet \
        --out-file tx.signed

And submit it:

.. code-block::bash
  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/node-genz-pool/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction submit \
        --tx-file tx.signed \
        --mainnet

Your stake key is now registered on the blockchain.

Note: --mainnet identifies the Cardano mainnet, for testnets use 
"--testnet-magic 1097911063" instead.
