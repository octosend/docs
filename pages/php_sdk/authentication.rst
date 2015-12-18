Authentication
--------------

First you need to authenticate against the Octosend API.

.. code-block:: php

  $username = '...';
  $password = '...';

  $client = new OctoSend\Client($username, $password);

This call returns a client object from which all calls to the API will be made.
This client is not connected, which means that the object does not store a permanent
connection to the server during the life cycle of the object but connects at needs
to perform the calls.

By default the SDK uses the production url of the API version matching the SDK release
(currently |api_current_version| at the url |api_current_url|). If you need, you
can provide a specific url to use by the constructor:

.. code-block:: php

  $url = 'https://api.octosend.com/api/v3.x/';

  $client = new OctoSend\Client($username, $password, $url);
