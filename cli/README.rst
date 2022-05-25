Cardano CLI
===================================================================

A CLI utility to support a variety of key material operations (genesis,
migration, pretty-printing..) for different system generations. Usage 
documentation can be found at cli/README.md.

The general synopsis is as follows:

.. code-blok:: bash
  
  docker run --interactive \
     genz-pool/cardano-cli \
         (Era based commands | Byron specific commands | Miscellaneous commands)

> NOTE: the exact invocation command depends on the environment. If you 
have only built cardano-cli, without installing it, then you have to
 prepend cabal run -- `` before ``cardano-cli. We henceforth assume 
 that the necessary environment-specific adjustment has been made, 
 so we only mention cardano-cli.

The subcommands are subdivided in groups, and their full list can be 
seen in the output of cardano-cli --help.


.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   docs/index.rst