.. cardano-pool-docker documentation master file, created by
   sphinx-quickstart on Wed May  4 07:18:14 2022.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to cardano-pool-docker's documentation!
===============================================================================

Visit genz-pool stake pool @ [genz-pool.com](https://genz-pool.com/stakeing/cardano).

From the official [cardano-node setup](https://docs.cardano.org/projects/cardano-node/en/latest/) tutorials 
from IOHK.
The container downloads and builds the [cardano-node](https://github.com/input-output-hk/cardano-node.git).

It can start either a block-producing node or a relay node, or both, and connect to the cardano 
network. By default it will connect to the test network, you can run on other networks 
using the CARDANO_NETWORK environment variable, See the [Environment variables](#environment) section.

If you want to run a stake pool, the block-producing container can take all the required steps 
to set up and register the stake pool.

Donate
-------------------------------------------------------------------------------

We hope you will find this project useful. If you like the work please consider 
delegating to genz-pool pool:

`[ARRA] Arrakis (c65ca06828caa8fc9b0bb015af93ef71685544c6ed2abbb7c59b0e62)`

or donating a few ADA to:

`addr1qys4rnfu5suydj480gwlnxxfkazjscy5j3ekgrnywvqht6ujn4up3dddmmul3a5p98996dyd5nhn2mwthwce6rjrp0esqtey6p`


.. toctree::
   :maxdepth: 1
   :caption: Contents:
   
   install
   node/README
   cli/README
   support


Indices and tables
===============================================================================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
