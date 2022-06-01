To Bech32
===============================================================================

The most common functionality is to encode in base 16. Here is an example

.. code-block:: bash

  $docker run --interactive \
     coincodile/cardano-bech32:latest \
     base16_ <<< 706174617465

It is possible to use any required base for incoding.

.. code-block:: bash
  
  $ docker run --interactive \
     coincodile/cardano-bech32:latest \
     base58_ <<< Ae2tdPwUPEYy

Here is an example that adds a custom prefix to encoded data:

.. code-block:: bash

  $ docker run --interactive \
     coincodile/cardano-bech32:latest \
     new_prefix <<< old_prefix1wpshgcg2s33x3
