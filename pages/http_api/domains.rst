Domains
-------

Domains list
~~~~~~~~~~~~

Count the available domains
"""""""""""""""""""""""""""

::

  GET https://api.octosend.com/api/3.0/domains/count

Retrieve the first ten domains
""""""""""""""""""""""""""""""

Body parameters

.. code-block:: json

  {
    "limit": 10
  }

sent on the *domains/fetch* method::

  POST https://api.octosend.com/api/3.0/domains/fetch

will return a JSON array with matching *domains* as objects with a *name* property:

.. code-block:: json

  [
    {
      "name": "my.domain.tld"
    }
  ]

Filtering
~~~~~~~~~

This example shows how to retrieve the list of the user's *domains* that contains
the string "demo." in the name of the *domain*. You just have to send:

.. code-block:: json

  {
    "limit": 100,
    "nameContains": "demo.",
    "offset": 0,
    "reverse": false
  }

on::

  POST https://api.octosend.com/api/3.0/domains/fetch

and you will be good.

As you have seen in the "first ten domains" example, you can send only the parameters you want.
The available parameters are:

======================= ==================== ===================================
Param name              Type                 Description
======================= ==================== ===================================
``nameContains``        string               a filter on the name of the domains

*Pagination params*
--------------------------------------------------------------------------------

``limit``               integer              the number of domains you want to
                                             retrieve

``offset``              integer              starting point to skip some domains

``reverse``             boolean              reverse order of the domains
                                             (alphabetical)
======================= ==================== ===================================
