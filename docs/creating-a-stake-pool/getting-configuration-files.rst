Get configuration files
===============================================================================

In both, legacy and docker based installation, you have to download latest version
of configurations.

Starting the node and connecting it to the network requires 3 configuration files:

* topology.json
* genesis.json
* config.json

You can download the configuration files from:

 `https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/index.html <https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/index.html>`_


From the CLI you can use

For Cardano testnet

.. code-block::bash
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/testnet-config.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/testnet-byron-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/testnet-shelley-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/testnet-alonzo-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/testnet-topology.json

For Mainnet:

.. code-block::bash
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/mainnet-config.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/mainnet-byron-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/mainnet-shelley-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/mainnet-alonzo-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/mainnet-topology.json

Starting the node uses the command "cardano-node run" and a set of options.

Get the complete list of available options with "cardano-node run --help"


.. code-block::bash
	--topology FILEPATH             The path to a file describing the topology.
  	--database-path FILEPATH        Directory where the state is stored.
  	--socket-path FILEPATH          Path to a cardano-node socket
  	--host-addr IP-ADDRESS          Optionally limit node to one IPv4 address
  	--host-ipv6-addr IP-ADDRESS     Optionally limit node to one IPv6 address
  	--port PORT                     The port number
  	--config NODE-CONFIGURATION     Configuration file for the cardano-node
  	--validate-db                   Validate all on-disk database files
  	--shutdown-ipc FD               Shut down the process when this inherited FD reaches EOF
  	--shutdown-on-slot-synced SLOT  Shut down the process after ChainDB is synced up to the
  	                                specified slot
    -h,--help                       Show this help text

Note: If you do not specify "--host-addr" nor "--host-ipv6-addr", node will use the 
default IPv4 and IPv6 addresses (depending which addresses are available).  If one 
specifies one of them only that address will be used, in particular if you only 
provide an IPv4 address, the node will not connect over IPv6.

To start a passive node:

.. code-block::bash
   docker run --interactive \
    --volume /path/to/node:/node \
    --volume /path/to/config:/config \
    gen2-pool/cardano-node run \
       --topology                 /config/mainnet-topology.json \
       --config                   /config/mainnet-config.json \
       --database-path            /node/db \
       --socket-path              /node/node.socket \
       --host-addr                x.x.x.x \
       --port                     3001 

NOTE: Replace x.x.x.x with your public IP and indicate the correct paths to the 
required files.

Many commands rely on the environment variable CARDANO_NODE_SOCKET_PATH which points
to the Linux socket related to the node. You make use this variable in other contaienrs
as:

.. code-block::bash
    export CARDANO_NODE_SOCKET_PATH=/node/node.socket

Check that the node is syncing by fetching the current tip. When syncing "slot" should 
be increasing.

.. code-block::bash
    cardano-cli query tip --mainnet

Where the result would be:

.. code-blcok::json
    {
        "epoch": 259,
        "hash": "dbf5104ab91a7a0b405353ad31760b52b2703098ec17185bdd7ff1800bb61aca",
        "slot": 26633911,
        "block": 5580350
    }

Note: --mainnet identifies the Cardano mainnet, for testnets use "--testnet-magic 1097911063" 
instead.
