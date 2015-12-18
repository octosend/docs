Installation
------------

Octosend follows standards and best practices for its PHP SDK and thus is available
both on its `octosend/php-sdk`_ github repository and through `composer`_ and the
`packagist package repository`_.

You can install the latest version with

.. code-block:: shell

  $ composer require octosend/php-sdk

Or add it as a dependency in your project *composer.json* file

.. code-block:: json

  {
    "require": {
      "octosend/php-sdk": "^1.0"
    }
  }

Then include the `composer`_ autoload file in your php script

.. code-block:: php

  include 'vendor/autoload.php';

While we recommend to use `composer`_ which is the standard dependency manager in the
PHP community you can also directly get the sources by cloning the `github sources`_

.. code-block:: shell

  $ git clone https://github.com/octosend/php-sdk.git
