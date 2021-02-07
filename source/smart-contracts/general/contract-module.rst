.. _contract-module:

=========================
Akıllı sözleşme modülleri
=========================

Akıllı sözleşmeler, *akıllı sözleşme modüllerindeki* zincirde konumlandırılır.

.. Not::

   Sade bir biçimde akıllı bir sözleşme modülü çoğunlukla * modül * olarak adlandırılır.

Bir modül, kodun sözleşmeler arasında paylaşılmasına izin veren bir veya daha
fazla akıllı sözleşme içerebilir ve opsiyonel olarak :ref:`contract schemas
<contract-schema>` içerebilir.

.. graphviz::
   :align: center
   :caption: İki akıllı sözleşme içeren akıllı bir sözleşme modülü.

   digraph G {
       subgraph cluster_0 {
           node [fillcolor=white, shape=note]
           label = "Module";
           "Crowdfunding";
           "Escrow";
       }
   }

Modül kendi kendine yeterli olmalıdır ve yalnızca zincirle etkileşime izin veren
kısıtlı bir içerik listesine sahip olmalidir. Bunlar, ana bilgisayar ortamı 
tarafından sağlanır ve ``concordium`` adlı bir modülü içe aktararak akıllı 
sözleşme için kullanılabilirler.

.. seealso::

   Check out :ref:`host-functions` for a complete reference.

Zincir üzerindek dil
====================

Concordium blockchain üzerinde akıllı sözleşme dili, taşınabilir bir derleme
hedefi olacak ve korumalı ortamlarda çalıştırılmak üzere tasarlanmış bir 
 `Web Assembly` (kısaca Wasm) alt kümesidir. Akıllı sözleşmelerin, ağdaki 
koda güvenmesi şart olmayan bakers tarafından yürütülecek olmasından dolayı 
bu yararlıdır.

Wasm düşük seviyeli bir dildir ve elle yazılması pratik değildir. Bunun yerine,
akıllı sözleşmeler daha yüksek seviyeli bir dilde yazılabilir ve bunlar daha 
sonra Wasm'da derlenebilir.

.. _WASM-KISITLAMALARI:

KISITLAMALAR
------------

.. todo::

   Add other limitations, such as start sections...

Blok zinciri ortamı, her düğümün sözleşmeyi tamamen aynı yolla ve tamamen aynı
miktarda kaynak kullanarak yürütebilmesi açısından çok özeldir. Aksi takdirde,
düğümler zincirin durumu ile ilgili fikir birliğine konusunda başarısız olurlar.
Bu nedenle akıllı sözleşmelerin sınırlı bir Wasm alt kümesinde yazılması gerekir.

Değişen nokta sayıları
^^^^^^^^^^^^^^^^^^^^^^

Although Wasm does have support for floating point numbers, a smart contract is
disallowed to use them. The reason for this is that Wasm floating-point numbers
can have a special ``NaN`` ("not a number") value whose treatment can result in nondeterminism.

The restriction applies statically, meaning that smart contracts cannot contain
floating point types, nor can they contain any instructions that involve floating
point values.


Deployment
==========

Deploying a module to the chain means submitting the module bytecode as a
transaction to the Concordium network. If *valid* this transaction will be
included in a block. This transaction, as every other transaction, has an
associated cost. The cost is based on the size of the bytecode and is charged
for both checking validity of the module and on-chain storage.

The deployment itself does not execute
smart contract. To execute, a user must first create an *instance* of a contract.

.. seealso::

   See :ref:`contract-instances` for more information.

.. _smart-contracts-on-chain:

.. _smart-contracts-on-the-chain:

.. _contract-on-chain:

.. _contract-on-the-chain:

Smart contract on the chain
===========================

A smart contract on the chain is a collection of functions exported from a deployed
module. The concrete mechanism used for this is the `Web Assembly`_ export
section. A smart contract must export one function for initializing new
instances and can export zero or more functions for updating the instance.

Since a smart contract module can export functions for multiple different smart
contracts, we associate the functions using a naming scheme:

- ``init_<contract-name>``: The function for initializing a smart contract must
  start with ``init_`` followed by a name of the smart contract. The contract
  must consist only of ASCII alphanumeric or punctuation characters, and is not
  allowed to contain the ``.`` symbol.

- ``<contract-name>.<receive-function-name>``: Functions for interacting with a
  smart contract are prefixed with the contract name, followed by a ``.`` and a
  name for the function. Same as for the init function, the contract name is not allowed
  to contain the ``.`` symbol.

.. note::

   If you develop smart contracts using Rust and ``concordium-std``, the
   procedural macros ``#[init(...)]`` and ``#[receive(...)]`` set up the
   correct naming scheme.

.. _Web Assembly: https://webassembly.org/
