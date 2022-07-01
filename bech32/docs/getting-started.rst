Getting Started
===============================================================================

At the first step, you must know all options from command line:


..code-block::bash

  docker run --interactive \
    genzbank/bech32  --help

This is a tool that converts to and from bech32 strings. Data are read 
from standard input.

The most common form of the command is:

..code-block::bash

  bech32 [PREFIX]


The PREFIX is an optional human-readable prefix (e.g. 'addr') that show
the format of the input data.
When provided, the input text is decoded from various encoding formats 
and re-encoded to bech32 using the given prefix.
When omitted, the input text is decoded from bech32 to base16.

This tool supports Base16, Bech32 & Base58 encoding formats.
