Getting Started
===============

.. _github project: https://github.com/octosend

Using Octosend is pretty simple and straightforward and you will get as much support
as you need from the Octosend team.

However you need to complete some steps to get your account and start sending emails.

Prerequisites
-------------

Account creation
~~~~~~~~~~~~~~~~

Whatever interface you will use to connect to Octosend and start using our shiny
features, you will always need an Octosend account. You can get one by registering
on the `Octosend`_ site or by contacting us directly at hello@octosend.com.

We will be happy to answer your questions and help you starting.

Domain delegation
~~~~~~~~~~~~~~~~~

Once your account is created, you are ready to connect to the customer area and
to use the Octosend API either directly through the HTTP endpoints or by other
interfaces.

Yet, you can not send real email. You need to perform some DNS configurations in order
to delegate one (or more) domain of your choice, one that you own of course.

Say your top level domain is *mydomain.tld*, you can decide to delegate the subdomain
*octosend.mydomain.tld*.

Every registrar and domain name provider has its own way to configure and delegate
a domain and/or subdomain, but the fundamental task is the same and follows the
same logic: point your domain to the octosend DNS services.

::

  octosend.mydomain.tld. IN NS ns1.rmta-services.com.
  octosend.mydomain.tld. IN NS ns2.rmta-services.com.
  octosend.mydomain.tld. IN NS ns3.rmta-services.com.

.. note::
  You can delegate and configure on Octosend as many domain as you want. It can
  give you the possibility to use specific domains on specific databases or for particular
  campaigns (let's say you want octosend.mydomain.tld but also newsletter.mydomain.tld,
  mail.mydomain.tld,... you can also have completely different domains configured,
  for example octosend.myotherdomain.tld,...)

.. important::
  Domain delegation is a best practice for email campaigns. The domain is yours and
  clearly identify where does your campaign come from. Your reputation can grow
  with this domain.
  The delegation allows us to tell the email service providers (those who provide
  email address to their users) which IP is authorized to send emails from this
  domain and manage IP, reputation and signatures for the best.

Choose your weapons
-------------------

Octosend features are intended for people who work with emails. We assume that most
of you will be perfectly able and willing to understand and control what they do.
For this, we offer several programmatic interfaces directly accessible from your
own code.

Nevertheless, building a complete graphical user interface and email management
software is not often a good solution and all of us want a good design and ergonomy
to check their statistics, understand and analyze what going on ... maybe program
the next campaign.
That is why we also have a solution for this and it is worth to take a glimpse at
our customer area.

.. note::
  Of course, you can use whatever solution you like as your main tool or in parallel.
  For example, you can wire Octosend on your own CRM solution through the HTTP API
  and still consult your statistics on your customer area.

HTTP API
~~~~~~~~

The HTTP API is the low level interface to access the Octosend features. It allows
a perfect control of the ins and outs of deliverability and email campaigns management
with Octosend.

It is reserved to advanced users who like to get their hands dirty and to understand
perfectly what they do. Furthermore, it is intended to control everything from your
side and your own softwares and codes.

You can find an :doc:`http_api` guide on this documentation and a complete
reference is available at https://octosend.com/#/api.

Software Development Kits (SDK)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Software development kits are HTTP API client implementations in some popular languages.
The abstractions provided by the SDKs allows you to access Octosend features way
more easily and to focus on your business.

It is the perfect compromise between getting ready quickly and easily and taking
control over Octosend and integrating it directly in your own softwares and business
layers.

All Octosend SDKs will be publicly released on the `github project`_.

PHP SDK
"""""""
The PHP SDK is our first implementation of an Octosend client in a specific language.
It comes from the needs of our users and is designed to make it easy for a PHP coder
to integrate easily our solution.

You can find the PHP SDK on its repository: https://github.com/octosend/php-sdk.
The :doc:`php_sdk` guide can be found on this documentation and its sources
are also publicly available on https://github.com/octosend/octosend-docs if you
feel the soul to contribute.

Python SDK (coming really soon)
"""""""""""""""""""""""""""""""
The python SDK is a core development of the Octosend team. We are currently working on
it. The implementation is done and the team is currently testing and finalizing
its work.

Let us know at hello@octosend.com if you want to be kept posted about the release
(before Christmas, that's for sure !! May be a Christmas gift for your boss and/or
your employees ?!).

Go SDK (coming soon)
""""""""""""""""""""
Fellow programmers are yet working on a Go SDK that should be released soon. Do
not hesitate to keep in touch directly at hello@octosend.com if you want more insight
on the advancement of this project.

.. warning::
  This project is community driven, so the Octosend team can not guarantee an accurate
  delivery date.

Node.js SDK (coming soon)
"""""""""""""""""""""""""
Fellow programmers are yet working on a node.js SDK that should be released soon. Do
not hesitate to keep in touch directly at hello@octosend.com if you want more insight
on the advancement of this project.

.. warning::
  This project is community driven, so the Octosend team can not guarantee an accurate
  delivery date.

----------------------

.. note::
  If you want to contribute as some already are, feel free to contact us. We will
  be happy to open you an account and help you build a new client in a rare or unused
  programming language (ok, that works for every Octosend related project too)!

.. important::
  We welcome all kind of contributions, either SDKs, plugins, ... developments that
  we can and will promote to our users, but also **feedbacks**, **issues tracking**,
  everything that can help us improve the product.
  Day to day, we work on this product with our users in mind and while we do our best,
  we need you to get better.

SMTP API (work in progress)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The SMTP API is designed to directly configure the SMTP transport protocol to use
in your code and the authentication and then trigger directly email from your code.

It is particularly used to trigger transactional emails, those you have to send
to confirm a payment or send login access for example.
The power of Octosend will still remain available through Octosend reserved SMTP headers.

This work is still in progress and will be released soon (another Christmas gift...
you will understand if you have read the whole page! otherwise, you can try and
enjoy the documentation search feature).

Customer Area
~~~~~~~~~~~~~

The customer area is available after log in on the Octosend website https://octosend.com.
It is a web interface that provides the service in the Software as a Service style.

It aims to be as ergonomic and easy to understand as possible which means we are
open to your feedback and that we will always improve it and likely fast! Go get
an eye on it and let us know if you like it (or not) at hello@octosend.com.
