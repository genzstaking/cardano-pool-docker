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
The first step is to create a certificate. The certificate will be registerd in
blockchain by a simple transaction.

.. code-block:: bash

  docker run --interactive \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      stake-address registration-certificate \
        --stake-verification-key-file stake.vkey \
        --out-file stake.cert

Get protocol parameters
-------------------------------------------------------------------------------
First of all, you must get the protocol parameters and store in a file. Get the 
protocol parameters and save them to protocol.json with:

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      query protocol-parameters \
        --mainnet \
        --out-file protocol.json


Get the transaction hash and index of the UTXO to spend
-------------------------------------------------------------------------------
The main ata is UTXO to spend. To get UTXO of the payment address, use the following
command:

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      query utxo \
        --address $(cat payment.addr) \
        --mainnet

Result is:

.. code-block:: bash

                             TxHash                                 TxIx        Amount
  --------------------------------------------------------------------------------------
  e88c26157adbd6fa5d0947ad031784d5486f38b3aaf1a87378708e937b98562a     0        20358943 lovelace + TxOutDatumNone

We will use TxHash#TxIx in the following sections.

Draft transaction
-------------------------------------------------------------------------------
Create a draft for the transaction and save it in tx.draft For the transaction 
draft, --tx.out, --invalid-hereafter and --fee can be set to zero.

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction build-raw \
      --tx-in e88c26157adbd6fa5d0947ad031784d5486f38b3aaf1a87378708e937b98562a#1 \
      --tx-out $(cat payment.addr)+0 \
      --invalid-hereafter 0 \
      --fee 0 \
      --out-file tx.draft \
      --certificate-file stake.cert

For --tx-in we use the following syntax: TxHash#TxIx where TxHash is the transaction 
hash and TxIx is the index; for --tx-out we use: TxOut+Lovelace where TxOut is the 
hex encoded address followed by the amount in Lovelace. For the transaction draft 
--tx-out, --invalid-hereafter and --fee can be set to zero.

Calculate fees
-------------------------------------------------------------------------------
The submision transaction needs one input, a valid UTXO from payment.addr, and 
an output that receives the change of the transaction. These are important in 
fee calculation.

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
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

    > 178701

Registering the stake address, not only pay transaction fees, but also includes a 
_deposit_ (which you get back when deregister the key) as stated in the protocol 
parameters:

The deposit amount can be found in the `protocol.json` under `stakeAddressDeposit`, 
for example in Shelley Mainnet:

.. code-block:: json

    "stakeAddressDeposit": 2000000,


Calculate the change to send back to payment address after including the deposit
-----------------------------------------------------------------------------------
In the transaction we must transfer 171485 + 2000000 Lovelace to the payment wallet.
So if you have a wallet with 1000000000 balance, following value must be keept in th
source wallet:

.. code-block:: bash

  expr 20358943 - 178701 - 2000000
  > 18180242

Submit the certificate with a transaction:
-------------------------------------------------------------------------------
Now you must transfer fee+deposit value from your main wallet and create a transaction.
Build the transaction, this time include  --invalid-hereafter and --fee

For the transaction, --tx.out need to be more approx. 1 ADA, --invalid-hereafter 
need to be set close ahead of the current slot. First: query the current slotnumber 
again to add it to --invalid-hereafter: 


.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      query tip --mainnet

The result is

.. code-block:: json

  {
      "era": "Alonzo",
      "syncProgress": "100.00",
      "hash": "aca5010c00d00c247023f79d3e189a3347befd97d7b7c145ba1cadc816f21f6c",
      "epoch": 341,
      "slot": 62353054,
      "block": 7312018
  }


Second we create a transaction

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction build-raw \
        --tx-in e88c26157adbd6fa5d0947ad031784d5486f38b3aaf1a87378708e937b98562a#1 \
        --tx-out $(cat payment.addr)+18180242 \
        --fee 178701 \
        --invalid-hereafter 62557477 \
        --out-file tx.raw \
        --certificate-file stake.cert

Sign it:

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
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

.. code-block:: bash

  docker run --interactive \
    --env CARDANO_NODE_SOCKET_PATH=/node/node.socket \
    --volume /mnt/genz-cardano/main-relay:/node \
    --volume $PWD:/root \
    --workdir /root \
    genz-pool/cardano-cli \
      transaction submit \
        --tx-file tx.signed \
        --mainnet

Your stake key is now registered on the blockchain.

Note: --mainnet identifies the Cardano mainnet, for testnets use 
"--testnet-magic 1097911063" instead.
