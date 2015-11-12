PHP SDK Integration
===================

.. _octosend/php-sdk: https://github.com/octosend/php-sdk
.. _github sources: https://github.com/octosend/php-sdk
.. _packagist package repository: https://packagist.org/packages/octosend/php-sdk
.. _composer: https://getcomposer.org/

Overview
--------

`Octosend`_ provides a service to create and manage emailing campaigns. The user
has one or several sending *domains* allowing him to create email campaigns (internally
known as *spoolers*), to follow how they process and perform and to retrieve
advanced *statistics*.

The PHP SDK is a PHP access layer on top of the HTTP API easing the use of the Octosend
features through an object oriented mapping hiding the raw calls to the API.

All calls are managed by a single client object and its methods as a single
straightforward entry point.

.. warning::
  All the examples of code provided are here to help understand how the PHP SDK works.
  They are not production ready code which requires to catch errors and exceptions
  and other thing that will have cluttered our guide with complexity.

Install
-------

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

Campaigns
---------

Campaign creation
~~~~~~~~~~~~~~~~~

Overview
""""""""

The first step is to choose the sending domain of the campaign. We directly retrieve
the domain object by the domain name.

.. code-block:: php

   // retrieve a connector to a specific domain
   $domain = $client->domain('demo.octosend.com');

Then we create a spooler object representing the campaign and we give it a name and
a start date.

.. code-block:: php

   // create and configure a campaign for this domain
   $spooler = $domain->createSpooler('marketing');

   // name of your choice, it allows you to recognize and search you campaign
   $spooler->name('my campaign for the first integration test');

   // give the campaign a timestamp as start date (24 hours later here)
   $spooler->start(time() + 86400);

Every spooler get an unique identifier called "token" allowing to directly access
the campaign later.

.. code-block:: php

   $token = $spooler->token();
   print_r($token);

The next step is to define the content of this campaign. We do that by creating a
message object for the spooler for which we specify several elements: the sender,
the subject line, the parts of the message,...

Once finished the message must be saved in the spooler.

.. code-block:: php

  $message = $spooler->message();

  $message->sender('John Doe');
  $message->subject('Nice shoes for the winter');

  $message->parts()->part(
    'text/html',
    '<html>here is my content</html>'
  );

  $message->parts()->part(
    'text/plain',
    'here is my text content'
  );

  // save your modification to the message in the spooler
  $message->save();

.. important::
  It is important you set both the HTML and the TEXT contents of your campaign with
  quality contents. While almost nobody (yes, some people with various goals in
  mind do it !) will read the TEXT version, it is always checked by the email address
  providers while checking for spams and may hurt your deliverability if not set
  with an appropriate content related to you HTML one!

The spooler is now configured and the content is set. We have to add recipients.
We create a mail with the recipient's email address and we add this mail to the spooler.
We say we "spool" a mail to the spooler.

.. code-block:: php

  $spooler->mail('octopus@deepsea.tld')->spool();

To optimize the number of calls to the API, it is always a good idea to batch add
recipients. This is pretty simple but requires to create a batch object, to add the
recipients to this batch object and then this object is spooled with only one call.

.. code-block:: php

   $batch = $spooler->batch();

   $batch->mail('snoopy@peanuts.tld');
   $batch->mail('charlie.brown@peanuts.tld');
   $batch->mail('woodstock@peanuts.tld');
   $batch->mail('flash@starlabs.tld');

   $batch->spool();

The last step (yes you are almost done!) is to flag the spooler as ready to be
send. The Octosend system will trigger the launch of the campaign at the given start
date.

.. code-block:: php

   $spooler->ready();

.. warning::
  After this call the campaign can not be modified anymore.

Tracking variables
""""""""""""""""""

The content of the message can embed tracking variables that will be dynamically
replaced (interpolated) when the mails will be formatted.
If needed, it allows to track the events occurring on the emails of this campaigns.

Variables appear between double brackets in the contents and can be the following:

* **unsubscribe**: an url allowing the recipients to unsubscribe from your list
* **mirror**: an url allowing to see the content online
* **click**: an url allowing to redirect the recipients to the given target url while tracking the link activation (click count)
* **pixel**: an HTML tag allowing to track the opening of the emails by the recipients

.. code-block:: php

  // message example with tracking
  $message = $spooler->message();

  $message->sender('John Doe');
  $message->subject('Nice shoes for the summer');

  $message->parts()->part(
    'text/html',
    "<html>here my html content with
      a <a href='{{unsubscribe}}'>unsubscribe link</a>,
      a <a href='{{mirror}}'>online version link</a>,
      a <a href='{{click:http://www.yourdomain.tld/you-page}}'>click tracking link</a>
      and finally a {{pixel}} to track the open
    </html>"
  );

   $message->parts()->part('text/plain', 'here is my text content');

   $message->save();

.. note::
  The {{pixel}} tracking variable is invisible and while is processed, does not
  appear to the user. You should not explicitly refer to it as we did in our
  example that will output : *"and finally a to track the open"*.

You can also redirect the unsubscribe link on an url on your side to display a custom
unsubscribe page.

.. code-block:: php

  $message->parts()->part(
    'text/html',
    "<html>here my html content with
      a <a href='{{unsubscribe:http://www.yourdomain.tld/your-unsubscribe-page}}'>
      redirecting unsubscribe link</a>,
    </html>"
  );

Customization variables
"""""""""""""""""""""""

You can also create your own variables to customize the content of a message. The
syntax is the same as for the tracking variables.

.. code-block:: php

   // message with tracking and customization
   $message = $spooler->message();

   $message->sender('John Doe');
   $message->subject('beautiful shoes for you, {{firstname}}');

   $message->parts()->part(
    'text/html',
    "<html>here my html content with
      a <a href='{{unsubscribe}}'>unsubscribe link</a>,
      a <a href='{{mirror}}'>online version link</a>,
      a <a href='{{click:http://www.yourdomain.tld/you-page}}'>click tracking link</a>
      a {{pixel}} to track the open
      and some variables to finish
      Dear {{firstname}} {{lastname}}
    </html>"
  );

  $message->parts()->part('text/plain', 'here is my text content');

  $message->save();

*{{firstname}}* and *{{lastname}}* will be replaced by their values for each email.
But where does those variables come from? The variables are linked for each recipients
when you create and spool the recipients email address:

.. code-block:: php

  // batch spool recipients with additional variables
  $batch = $spooler->batch();

  $mail = $batch->mail('charlie.brown@peanuts.tld');
  $message = $mail->message();
  $message->variables([
    'firstname' => 'Charlie',
    'lastname' => 'Brown'
  ]);

  $mail = $batch->mail('flash@starlabs.tld');
  $message = $mail->message();
  // oooops !
  $message->variables([
    'firstname' => 'Barry',
    'lastname' => 'Allen'
  ]);

  $batch->spool();

Retrieve a campaign
~~~~~~~~~~~~~~~~~~~

At the creation, each campaign is assigned an unique identifier:

.. code-block:: php

  $token = $spooler->token();

This token identifier can be saved on the client side and allows to retrieve the campaign
later:

.. code-block:: php

  $spooler = $client->spooler($token);

Statistic and results
---------------------

Once your campaign is launched, you can retrieve statistics for this campaign.
You first retrieve the campaign object.

You then need to specify the aggregation type you desire. The aggregation type changes
the results you get from the API calls and group the data according to it.

Aggregation can be: *router*, *router+domain*, *router+provider*, *domain*,
*provider*, *global*

* **router**: routers are the entities that route your emails. It can be the Octosend own routers or third party routers used with your agreement to improve the performance of your campaigns
* **domain**: domain in this context represents the recipients domains (yahoo.fr, yahoo.com, orange.fr,...)
* **provider**: provider represents the recipients high level domains grouped by entities (Microsoft = outlook.fr, outlook.com, hotmail.fr, etc.)
* **router+domain**: by router as described before, then by domain
* **router+provider**: by router then by provider

.. code-block:: php

  $aggregation = 'router+provider'; // or another one of the above

  $statistics = $spooler->statistics($aggregation);
  print_r($statistics);

You will get several counters of elements and events that occurred during you campaign
processing.

In "**activity**", you will find the following activity counters:

* **open**: number of recipients that have opened the email
* **click**: number of recipients that have clicked a link in the email
* **mirror**: number of recipients that are gone to the online version

In "**routing**", you will find the following routing counters:

* **ok**: number of messages delivered to their recipients
* **tempfail**: number of messages that could not be delivered but will be tried again later (ISP network issues,...)
* **permfail**: number of messages that could not be delivered at all (non-existent addresses or domains,...)

In "**spooling**", you will find the following counters about your spooler:

* **count**: total number of messages spooled
* **blocked**: number of messages blocked for preventive reasons (preserve the ip, sending domain and campaign reputation from global blocking - spamtraps, already known non-existent addresses,...)
* **quarantine**: number of messages in quarantine
