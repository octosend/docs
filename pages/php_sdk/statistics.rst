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

In "**routing**", you will find the following routing counters:

* **ok**: number of messages delivered to their recipients
* **tempfail**: number of messages that could not be delivered but will be tried again later (ISP network issues,...)
* **permfail**: number of messages that could not be delivered at all (non-existent addresses or domains,...)

In "**spooling**", you will find the following counters about your spooler:

* **count**: total number of messages spooled
* **blocked**: number of messages blocked for preventive reasons (preserve the ip, sending domain and campaign reputation from global blocking - spamtraps, already known non-existent addresses,...)
* **quarantine**: number of messages in quarantine
