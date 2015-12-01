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
