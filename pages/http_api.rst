HTTP API Integration
====================

.. _API reference: https://octosend.com/#/api
.. _cURL: http://curl.haxx.se/
.. _HTTPie: http://httpie.org/
.. _Postman: https://www.getpostman.com/

Overview
--------

`Octosend`_ provides a service to create and manage emailing campaigns. The user
has one or several sending *domains* allowing him to create email campaigns (internally
known as *spoolers*), to follow how they process and perform and to retrieve
advanced *statistics*.

The HTTP API is the low level interface to the Octosend service and allows a fine
control over the email sending operations. Advanced users can rely on the HTTP API
to integrate Octosend to their own softwares or to code SDK, plugins and extensions.

Versions and reference
~~~~~~~~~~~~~~~~~~~~~~

Each version of the API has its own url. The current stable version is |api_current_version|
and is hosted at the url |api_current_url|. The API exposes its methods as paths
relative to this url.

The `API reference`_ lists and documents all the methods available for a version of
the API. While pretty arid at first, you will find that the API is not so hard to
tackle. This integration documentation will guide you through common tasks allowing
you to get familiar with the concepts and logic exposed.

Design
~~~~~~

Global design
"""""""""""""

While not strictly a REST API, the Octosend HTTP API can be considered RESTful as
it exposes **resources** through urls. For each resources, data and states (actions)
can be accessed and retrieved.
For the moment, the API mainly uses the HTTP verbs **GET** and **POST** to perform
specific tasks.

The format used by the Octosend API is **JSON** either for the requests and the responses
(JSON body).

Resources
"""""""""

The resources exposed by the API are:

============================ ===================================================
``domain`` and ``domains``   Octosend allows you to use several sending
                             domains and this resource allows to manage them

``spooler`` and ``spoolers`` the spoolers represents you emailing campaigns,
                             the abstraction in which you add all the
                             informations for a specific campaign

``statistics``               a shortcut entry point to access the metrics about
                             your sending activity

``events``                   for each email (it means for each specific content
                             sent to a given email address), you have access to
                             events of several types such as routing or activity
                             events. They are triggered when our routers, the
                             email address providers or the recipients do some
                             actions on the email.

``timeline``                 a convenience that provide a timed aggregated
                             representation of your metrics

``scoring``                  scoring is a resource able to note the elements you
                             send to it

``templates``                you can manage templates to recall later to build
                             your contents
============================ ===================================================

Some of those resources are business objects meant to be created (spoolers,
domains, templates) while others allow to access data about those resources built
during the routing process (scoring, statistics, timeline, events).

For the business objects the calls available on the collections of resources
always adhere the following logic:

======================= ========== ================ ============================
Method                  HTTP verb  Example          Description
======================= ========== ================ ============================
``/<resources>/create`` POST       /spoolers/create create a new resource
                                                    instance

``/<resources>/fetch``  POST       /spoolers/fetch  retrieves an array of resource
                                                    instances


``/<resources>/count``  GET        /spoolers/count  count all the existing instances
                                                    of the resource

``/<resources>/count``  POST       /spoolers/count  count all the existing instances
                                                    of the resource matching the
                                                    given criteria

======================= ========== ================ ============================

Each type of resource has its own methods and specificities so we invite you to
read the examples below to see how to perform some common operation or to check
the full `API reference`_ to find what you are looking for.

Nevertheless, if you can not find how to do what you want do not hesitate to tell
us on hello@octosend.com and we will enrich this documentation with examples to
perform the task. Finally, you can ask for help on support@octosend.com and our
support team will do its best to help you.

.. important::
  Seriously, we really want to improve this documentation and will be more than
  happy to know your wish and requirements! You will receive a very quick answer
  and see that words will always be followed by deeds.


.. _api-errors:

Errors
""""""

The API uses HTTP code for standard errors. The main errors are:

========= ============ =========================================================
Code      Error        Description
========= ============ =========================================================
``400``   Bad Request  this code is mainly returned when there is an error
                       in your body or other request elements

``403``   Forbidden    this code is returned when you are not allowed to
                       call a resource (see :ref:`authentication`)

``404``   Not Found    in the context of an API, it often means the
                       resource (or method) called does not exist
========= ============ =========================================================

Additionally, you will get a JSON response with more details about the error, allowing
a human or a program to process and log the errors.

HTTP Clients
~~~~~~~~~~~~

Last words before to begin some calls. By design an HTTP API can be consumed by every
http clients. This includes command line clients such as `cURL`_ or `HTTPie`_, or programing languages
specific standard or third party libraries.
For testing purpose you can also use browser plugins such as `Postman`_ that will
let you test calls directly from you browser.

.. warning::
  All the examples of code provided are here to help understand how the HTTP API works.
  They are not production ready code and need to be performed with the HTTP client
  of you choice, error management, data persistence,...

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

Campaign creation
-----------------

Overview
~~~~~~~~

Create a campaign
"""""""""""""""""

The first step is to create an empty spooler representing the campaign on a specific
domain.

Send the body

.. code-block:: json

  {
    "domain": "my.domain.tld",
    "type": "marketing"
  }

on the *spoolers/create* method::

  POST https://api.octosend.com/api/3.0/spoolers/create

This will return the representation of a new empty *spooler*:

.. code-block:: json

  {
    "archived": false,
    "creation": 1,
    "domain": "my.domain.tld",
    "name": "",
    "start": 0,
    "state": "new",
    "token": "unique-idenfifier",
    "type": "marketing"
  }

where the *token* property is a hash unique identifier of your *spooler* you will
use to perform every other calls on the *spooler* resource.

.. note::
  Every call to a *spooler* method will return this complete representation of the
  given spooler.

Set the name of the campaign
""""""""""""""""""""""""""""

Let's add some more information on your spooler. First set the name with:

.. code-block:: json

  {
    "name": "Your spooler name"
  }

on::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/name

.. warning::
  The *name* is an important property to set in your workflow. It will allow you to
  identify the specific *spooler* in your own words and to search and retrieve it later.
  By following some convention of your own, you will be able to get what you want
  easily.

Set the start date
""""""""""""""""""

Then a start date with:

.. code-block:: json

  {
    "start": <start-date-timestamp>
  }

on::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/start

.. note::
  If you provide the current timestamp as *start* date, the activation of the campaign
  with the */spooler/<current-spooler-token>/ready* method will send the campaign
  immediately while you can program your campaign to start at a specific date in
  the future (do not forget to activate your campaign still, more on that later).

Add your message
""""""""""""""""

The next step is to define the content of this campaign. We do that by creating a
*message* for which we specify several elements: the sender, the subject line, the
parts of the message,...

The first thing to do is to set the contents that will be parts of the message:

.. code-block:: json

  {
    "type": "text/html",
    "content": "<html>here is my html content</html>"
  }

to the *spooler* *resources/part* method::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/resources/part

and your HTML content is set. The response return a string token that is the internal
link to the posted content. It will be used later to set the parts to include in the
message, so store it somewhere. For the example, we will call it::

  <html-content-part-token>

Now set the TEXT content:

.. code-block:: json

  {
    "type": "text/plain",
    "content": "here is my text content",
  }

to the same *resources/part* method::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/resources/part

and we will call this token::

  <text-content-part-token>

.. important::
  It is important you set both the HTML and the TEXT contents of your campaign with
  quality contents. While almost nobody (yes, some people with various goals in
  mind do it !) will read the TEXT version, it is always checked by the email address
  providers while checking for spams and may hurt your deliverability if not set
  with an appropriate content related to you HTML one!

You contents are ready, so you can build your message with all the elements by specifying
them in the body of a call:

.. code-block:: json

  {
    "sender": "John Doe",
    "subject": "Nice shoes for the winter",
    "parts": [
      "<html-content-part-token>",
      "<text-content-part-token>"
    ]
  }

that you can send on the *spooler* *message* method::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/message

Add some recipients
"""""""""""""""""""

The spooler is now configured and the content is set. We have to add recipients.
We create a mail with the recipient's email address and we add this mail to the spooler.
We say we "spool" a mail to the spooler.

.. code-block:: json

  {
    "mails": [
      { "email": "octopus@deepsea.tld" }
    ]
  }

To optimize the number of calls to the API, it is always a good idea to batch add
recipients. This is pretty simple as it only requires to populate the *mails* array.

.. code-block:: json

  {
    "mails": [
      { "email": "snoopy@peanuts.tld" },
      { "email": "charlie.brown@peanuts.tld" },
      { "email": "woodstock@peanuts.tld" },
      { "email": "flash@starlabs.tld" }
    ]
  }

Just send this body to the right *spool* method::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/spool

and your recipients are set.

Send the campaign
"""""""""""""""""

The last step (yes you are almost done!) is to flag the spooler as ready to be
send.

.. note::
  Note that before this final step that lock the campaign and set it as ready,
  you can test your work at any time. It allows you to check how your message render
  on several email clients, to check the validity of the links or to get the validation
  for the final shoot. See :ref:`campaigns-testing` for more information.

The Octosend system will trigger the launch of the campaign at the given start
date::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/ready

.. warning::
  After this call the campaign can not be modified anymore. Do not forget
  :ref:`campaigns-testing`


Tracking variables
~~~~~~~~~~~~~~~~~~

The content of the message can embed tracking variables that will be dynamically
replaced (interpolated) when the mails will be formatted.
If needed, it allows to track the events occurring on the emails of this campaigns.

Variables appear between double brackets in the contents and can be the following:

* **unsubscribe**: an url allowing the recipients to unsubscribe from your list
* **mirror**: an url allowing to see the content online
* **click**: an url allowing to redirect the recipients to the given target url while tracking the link activation (click count)
* **pixel**: an HTML tag allowing to track the opening of the emails by the recipients

All occurs in the *resources/part* method we already seen before::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/resources/part

with the variables in the content string

.. code-block:: json

  {
    "type": "text/html",
    "content": "<html>here my html content with
      a <a href='{{unsubscribe}}'>unsubscribe link</a>,
      a <a href='{{mirror}}'>online version link</a>,
      a <a href='{{click:http://www.yourdomain.tld/you-page}}'>click tracking link</a>
      and finally a {{pixel}} to track the open
    </html>"
  }

and you are done!

.. note::
  The {{pixel}} tracking variable is invisible and while is processed, does not
  appear to the user. You should not explicitly refer to it as we did in our
  example that will output : *"and finally a to track the open"*.

You can also redirect the unsubscribe link on an url on your side to display a custom
unsubscribe page.

.. code-block:: json

  {
    "type": "text/html",
    "content": "<html>here my html content with
      a <a href='{{unsubscribe:http://www.yourdomain.tld/your-unsubscribe-page}}'>
      redirecting unsubscribe link</a>,
    </html>"
  }

Customization variables
~~~~~~~~~~~~~~~~~~~~~~~

You can also create your own variables to customize the content of a message. The
syntax is the same as for the tracking variables.

Content with tracking and customization:

.. code-block:: json

  {
    "type": "text/html",
    "content": "<html>here my html content with
      a <a href='{{unsubscribe}}'>unsubscribe link</a>,
      a <a href='{{mirror}}'>online version link</a>,
      a <a href='{{click:http://www.yourdomain.tld/you-page}}'>click tracking link</a>
      and finally a {{pixel}} to track the open
      and some variables to finish
      Dear {{firstname}} {{lastname}}
    </html>"
  }

Customization variables can also be used in subject line:

.. code-block:: json

  {
    "sender": "John Doe",
    "subject": "beautiful shoes for you, {{firstname}}",
    "parts": [
      "<html-content-part-token>",
      "<text-content-part-token>"
    ]
  }

*{{firstname}}* and *{{lastname}}* will be replaced by their values for each email.
But where does those variables come from? The variables are linked for each recipients
when you create and spool the recipients email address:

.. code-block:: json

  {
    "mails": [
      {
        "email": "charlie.brown@peanuts.tld",
        "variables": {
          "firstname": "Charlie",
          "lastname": "Brown"
        }
      },
      {
        "email": "flash@starlabs.tld"
        "variables": {
          "firstname": "Barry",
          "lastname": "Allen"
        }
      }
    ]
  }

.. _campaigns-testing:

Test your campaigns
~~~~~~~~~~~~~~~~~~~

Depending on your workflow, there is good chances you want to test how your
campaigns render before to seal your fate and send your message to all your
targeted recipients.

Octosend provides an easy way to do it with a special method you can call at any
time while editing your campaign (as long as you did not flag your campaign as
ready).

You can do it by calling::

  POST https://api.octosend.com/api/3.0/spooler/<current-spooler-token>/draft

with the list of test emails on which you want to send the current version of your
message:

.. code-block:: json

  {
    "mails": [
      {
        "email": "head@peanuts.tld",
        "variables": {
          "firstname": "Charlie",
          "lastname": "Brown"
        }
      },
      {
        "email": "head-of-marketing@peanuts.tld"
        "variables": {
          "firstname": "Pal",
          "lastname": "Patine"
        }
      }
    ]
  }

The method works exactly as the *spool* method except that the emails are sent
immediately.

.. warning::
  The test feature is only available to validate a campaign and make it "good for
  shooting". Therefore the is strong limitations on what you can do ... or not.
  For instance, you are limited to 10 emails per draft method call.



Campaigns retrieval
-------------------

Retrieve a campaign
~~~~~~~~~~~~~~~~~~~

At the creation, each campaign is assigned an unique identifier. This token identifier
can be saved on the client side and allows to retrieve the campaign later::

  GET https://api.octosend.com/api/3.0/spooler/<spooler-token>

That will return the classic JSON representation of your campaign:

.. code-block:: json

  {
    "archived": false,
    "creation": 0,
    "domain": "my.domain.tld",
    "name": "My campaign",
    "start": 5806512000,
    "state": "finished",
    "token": "unique-identifier",
    "type": "marketing"
  }

List the first ten campaigns
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Body parameters

.. code-block:: json

  {
    "limit": 10
  }

sent on the *spoolers/fetch* method::

  POST https://api.octosend.com/api/3.0/spoolers/fetch

will return a JSON array with matching *spoolers* as objects with a spoolers properties:

.. code-block:: json

  [
    {
      "domain": "my.domain.tld",
      "name": "Awesome octopus to send your emails",
      "tags": [],
      "archived": 0,
      "creation": 1440767231,
      "state": "new",
      "start": 1440767231,
      "token": "<current-spooler-token>",
      "type": "marketing"
    }
  ]

Filtering
~~~~~~~~~

The example shows how to retrieve the first ten *new* *marketing* campaigns from the domain *my.domain.tld*
containing *"promotion"* in their names:

.. code-block:: json

  {
    "createdAfter": 0,
    "createdBefore": 0,
    "domains": [
      "my.domain.tld"
    ],
    "endAfter": 0,
    "endBefore": 0,
    "limit": 10,
    "nameContains": "promotion",
    "offset": 0,
    "reverse": false,
    "startAfter": 0,
    "startBefore": 0,
    "states": [
      "new"
    ],
    "types": [
      "marketing"
    ]
  }

While this body provides the full list of parameters for the example, you can off course,
send only the parameters you want. The available parameters are:

======================= ==================== ===================================
Param name              Type                 Description
======================= ==================== ===================================
*Spoolers properties*
--------------------------------------------------------------------------------

``nameContains``        string               a filter on the name of the spoolers

``domains``             strings array        an array of exact domains names

``states``              strings array        an array of spoolers status. The
                                             currently available states are
                                             *"new"*, *"pending"*, *"running"*,
                                             *"cancelled"*, *"finished"*

``types``               strings array        an array of types you are searching
                                             for. The currently available types
                                             are *"marketing"* or *"transactional"*

*Pagination params*
--------------------------------------------------------------------------------

``limit``               integer              the number of spoolers you want to
                                             retrieve

``offset``              integer              starting point to skip some spoolers

``reverse``             boolean              reverse order of the spoolers
                                             (creation date)

*Date params*
--------------------------------------------------------------------------------

``createdBefore``       integer (timestamp)  max timestamp boundaries on the
                                             creation date

``createdAfter``        integer (timestamp)  min timestamp boundaries on the
                                             creation date

``startBefore``         integer (timestamp)  max timestamp boundaries on the
                                             start date

``startAfter``          integer (timestamp)  min timestamp boundaries on the
                                             start date

``endBefore``           integer (timestamp)  max timestamp boundaries on the
                                             end date

``endAfter``            integer (timestamp)  min timestamp boundaries on the
                                             end date

======================= ==================== ===================================

Statistic and results
---------------------

Once your campaign is launched, you can retrieve statistics for this campaign.

The statistics resource propose several aggregation type. The aggregation type changes
the results you get from the API calls and group the data according to it.

Aggregation can be: *router*, *router+domain*, *router+provider*, *domain*,
*provider*, *global*

* **router**: routers are the entities that route your emails. It can be the Octosend own routers or third party routers used with your agreement to improve the performance of your campaigns
* **domain**: domain in this context represents the recipients domains (yahoo.fr, yahoo.com, orange.fr,...)
* **provider**: provider represents the recipients high level domains grouped by entities (Microsoft = outlook.fr, outlook.com, hotmail.fr, etc.)
* **router+domain**: by router as described before, then by domain
* **router+provider**: by router then by provider

For example call::

  POST https://api.octosend.com/api/3.0/statistics/spooler/<spooler-token>

with

.. code-block:: json

  {
    "groupBy": "router+provider"
  }

You will get a JSON object with several counters of elements and events that occurred
during you campaign processing.

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

.. note::
  After this guide you should be more confident to walk through the `API Reference`_
  and understand better how the API works. If you have any question or feedback about
  `Octosend`_ or this documentation the team will be happy to ear them at hello@octosend.com.
  This documentation is a permanent work we want to improve and enrich with your
  feedbacks. You can also contribute directly to the documentation project, so feel
  free to take a look and send your pull requests at https://github.com/octosend/octosend-docs.
