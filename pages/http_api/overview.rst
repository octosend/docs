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
