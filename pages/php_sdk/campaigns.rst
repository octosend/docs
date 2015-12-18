Campaign creation
-----------------

Overview
~~~~~~~~

The first step is to choose the sending domain of the campaign. We directly retrieve
the domain object by the domain name.

.. code-block:: php

   // retrieve a connector to a specific domain
   $domain = $client->domain('demo.octosend.com');

Create a campaign
"""""""""""""""""

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

Set the content
"""""""""""""""

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

Add recipients
""""""""""""""

The spooler is now configured and the content is set. We have to add recipients.
We create a mail with the recipient's email address and we add this mail to the spooler.
We say we "spool" a mail to the spooler.

.. code-block:: php

  $spooler->mail('octopus@deepsea.tld')->spool();

.. _batch-recipients:

Batch add recipients
""""""""""""""""""""

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

Send the campaign
"""""""""""""""""

The last step (yes you are almost done!) is to flag the spooler as ready to be
send. The Octosend system will trigger the launch of the campaign at the given start
date.

.. note::
  Note that before this final step that lock the campaign and set it as ready,
  you can test your work at any time. It allows you to check how your message render
  on several email clients, to check the validity of the links or to get the validation
  for the final shoot. See :ref:`campaigns-testing` for more information.

.. code-block:: php

   $spooler->ready();

.. warning::
 After this call the campaign can not be modified anymore. Do not forget
 :ref:`campaigns-testing`


.. _campaigns-testing:

Test your campaigns
~~~~~~~~~~~~~~~~~~~

Depending on your workflow, there is good chances you want to test how your
campaigns render before to seal your fate and send your message to all your
targeted recipients.

Octosend provides an easy way to do it with a special method you can call at any
time while editing your campaign (as long as you did not flag your campaign as
ready).

You can test on every email created on a spooler with its `draft` method:

.. code-block:: php

  $spooler->mail('octopus@deepsea.tld')->draft();

In the same way you spooled several email previous in :ref:`batch-recipients`,
you can test on several emails in one call, which is highly recommended.

.. code-block:: php

  $batch = $spooler->batch();

  $batch->mail('snoopy@peanuts.tld');
  $batch->mail('charlie.brown@peanuts.tld');
  $batch->mail('woodstock@peanuts.tld');
  $batch->mail('flash@starlabs.tld');

  $batch->draft();

As you can see `spool` and `draft` methods are pretty close. The huge difference
is the goal of each method and so how email are sent. `draft` send immediately
when the call is performed to a limited number of recipient during the campaign
creation. `spool` on its side only send email to the recipients when `ready` is
called on a spooler and at the start date set.

.. warning::
  The test feature is only available to validate a campaign and make it "good for
  shooting". Therefore the is strong limitations on what you can do ... or not.
  For instance, you are limited to 10 emails per draft method call.

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
~~~~~~~~~~~~~~~~~~~~~~~

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


Attachments
~~~~~~~~~~~

If you want to email invoices, payment receipts or other attachments meaningful
for your clients, you can add those as part of the message for each email.

.. note::
  In theory, you can send the same attachments to all you recipients through the
  spooler global message, but in most of the case, it will be a bad practice as
  design and other images used in the email should not be embedded as attachments
  but preferably hosted online.

First you create your mail with the spooler or with the batch spooler depending
on the way you manage your recipient (let us remind that batch is the preferred
way to do it). Then you get the message associated to this mail and finally the
parts of this message to add the attachment part as raw content of you file.

.. warning::
  Attachments are limited is size, type and context. First the attachments does not
  make sense for marketing campaigns and so are limited to **transactional emails**.
  Then the max size of the attachment is **2Mo**, so we encourage you to keep your
  files small (nobody wants to receive a huge file in its inbox, except from
  friends maybe...). Finally, only the **following types** are allowed: *application/pdf*,
  *application/json*, *image/jpeg*, *text/plain*, *text/html*. If it is a problem
  for you, do not hesitate to contact us to tell us about your needs.

.. code-block:: php

  $mail = $batch->mail('charlie.brown@peanuts.tld');
  // or $spooler->mail('charlie.brown@peanuts.tld');

  $message = $mail->message();

  // add your contents for each recipient, here charlie
  $message->parts()->part('text/html', '...');
  $message->parts()->part('text/plain', '...');

  $message->parts()->attachment(
      'application/pdf',
      file_get_contents('202001_charlie-brown_invoice.pdf'),
      'invoice.pdf'
  );

  $mail = $batch->mail('flash@starlabs.tld');
  $message = $mail->message();
  // add your contents for each recipient, here flash
  $message->parts()->part('text/html', '...');
  $message->parts()->part('text/plain', '...');
  $message->parts()->attachment(
      'application/pdf',
      file_get_contents('202001_barry-allen_invoice.pdf'),
      'invoice.pdf'
  );

.. important::
  As adding an attachment redefine the message for each recipient, you need to
  provide all the message parts, including the html and text contents, tags,...
  The recipient defined message **does not use the spooler message**.

.. danger::
  If you get an exception while you think that everything is ok, first check the
  type of your campaign: as said earlier, **attachments are forbidden on marketing
  campaign**. Make sure your current spooler is *transactional*.

.. tip::
  This method will work as soon as you get a mail, so this is the case for spooler
  mail creation and batch mail creation, but also in the context of draft and
  campaign testing.


Campaigns retrieval
-------------------

Retrieve a campaign
~~~~~~~~~~~~~~~~~~~

At the creation, each campaign is assigned an unique identifier:

.. code-block:: php

  $token = $spooler->token();

This token identifier can be saved on the client side and allows to retrieve the campaign
later:

.. code-block:: php

  $spooler = $client->spooler($token);
