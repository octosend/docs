.. _authentication:

Authentication
--------------

The authentication is a two step process. The first step is to explicitly call
the *authenticate* method with you credentials as parameters.

The JSON body request is:

.. code-block:: json

  {
    "username": "woodstock",
    "password": "twit***stoleme"
  }

where you need to put your own *username* and *password*.
When you call the *authenticate* method with this body and valid credentials::

  POST https://api.octosend.com/api/3.0/authenticate

you will get the following JSON answer:

.. code-block:: json

  {
    "api-key": "hash-string",
    "entity": "Peanuts Inc",
    "username": "woodstock"
  }

The *api-key* is the param you will use as the value of *X-RMTA-API-Key* for all
other methods. **As a second step you send the *X-RMTA-API-Key* header for every
method.**

.. warning::
  In the following content, we will not tell you to send the *X-RMTA-API-Key* header
  again in order to simplify the explanations for each specific method.
  Don't forget to add it or your calls will always receive a *403 HTTP response*
  (see :ref:`api-errors`).
