Creating a stake pool
===============================================================================

Prerequisites for stake pool operators
-------------------------------------------------------------------------------
As a stake pool operator for Cardano, you will typically have the following 
abilities:

* operational knowledge of how to set up, run and maintain a Cardano node continuously
* a commitment to maintain your node 24/7/365
* system operation skills
* server administration skills (operational and maintenance).
* experience of development and operations (DevOps) would be very useful


Hardware requirements
-------------------------------------------------------------------------------
In terms of hardware, you should ensure you have the following:

* 10 GB of RAM
* 24 GB of hard disk space
* a good network connection and about 1 GB of bandwidth per hour
* a public IP4 address

Note that processor speed is not a significant factor for running a stake pool.


Stake pool installation instructions
-------------------------------------------------------------------------------
To learn how to set up your own stake pool, follow the installing the node from 
source instructions. However, I try to replace the legacy installation with docker
images. So, you just need to install docker on your own machine.

You can then proceed with the following operational tasks for managing your stake 
pool. I use docker as the basic tools in all steps:

.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   creating-a-stake-pool/getting-configuration-files.rst
   creating-a-stake-pool/create-key-and-addresses.rst
   creating-a-stake-pool/create-a-simple-transaction.rst
   creating-a-stake-pool/register-stake-address-on-the-blockchain.rst
   
   
   creating-a-stake-pool/registering-a-stake-pool-with-metadata.rst



