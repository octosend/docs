
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
