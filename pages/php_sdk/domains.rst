Domains
-------

Domains list
~~~~~~~~~~~~

Count the available domains

.. code-block:: php

   $n = $client->domains()->count();
   print_r($n);


Retrieve the first ten domains

.. code-block:: php

  $domains = $client->domains()->fetch(0, 10);

  foreach($domains as $domain) {
    print_r($domain->name());
  }


Filtering
~~~~~~~~~

This example shows how to retrieve the list of the user's domains that contains
the string "demo." in the name of the domain

.. code-block:: php

  $filter = $client->domains();
  $filter->nameContains('demo.');

  print_r($filter->count());

  $domains = $filter->fetch(0, 10);
  foreach($domains as $domain) {
    print_r($domain->name());
  }
