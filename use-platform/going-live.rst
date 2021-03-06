Going Live
==========

When your site is ready to go live, you need to configure the routes of your project and your DNS provider to point your domain to Platform.sh.

1 - Domain
----------

First thing you need is to add your domain via the :ref:`platform_ui` using the configuration page of your project. It is important to note that once you do this, your site master branch will no longer be accessible at master-<project_id>.<country>.platform.sh. If you require access to the site, you can create a hosts file entry and point it to the IP address that resolves when you access your master project branch.

2 - Routes
----------

You can configure the :term:`routes` of your project directly within the :ref:`platform_ui` or within your ``.platform/routes.yaml`` file.

.. seealso::
   * :ref:`routes_configuration`

Single hostname
^^^^^^^^^^^^^^^

If you want your site to only be available at ``http://mydomain.com`` and have ``http://www.mydomain.com`` redirect to ``http://mydomain.com``, you need define your ``routes.yaml`` as follow:

.. code-block:: console
    
    "http://{default}/":
        type: upstream
        upstream: "php:php"

    "http://www.{default}/":
        type: redirect
        to: "http://{default}/"

Multiple hostnames
^^^^^^^^^^^^^^^^^^

If you want your site to be available at both ``http://mydomain.com`` and ``http://www.mydomain.com``, you need to define one upstream for each hostname.

Here would be an example of your ``routes.yaml`` for the ``http://mydomain.com`` URL:

.. code-block:: console
    
    "http://{default}/":
        type: upstream
        upstream: "php:php"

    "http://www.{default}/":
        type: upstream
        upstream: "php:php"

.. note::
  You can test those routes on your development environments with:

  * ``http://[branch]]-[project-id].[cluster].platform.sh``
  * ``http://www-[branch]]-[project-id].[cluster].platform.sh``

.. _https:

HTTPS
^^^^^

If you have uploaded your SSL certificate and you want to serve your entire site with HTTPS, here is what your routes would look like:

.. code-block:: console
    
    "https://{default}/":
        type: upstream
        upstream: "php:php"

    "http://{default}/":
        type: redirect
        to: "https://{default}/"

.. seealso::
   * :ref:`routes_configuration`

.. _dns:

3 - DNS
-------

Configure your DNS provider to point your domain to your `Platform.sh <https://platform.sh>`_  Master environment.

Once you've checked with your registrar about where to change your DNS settings, add a CNAME record that references the Master environment's hostname: ``<environment>-<project>.<cluster>.platform.sh``

If you use multiple hostnames for your site, you need to add a CNAME record for each of them. For example: ``master-k4ywtmwigmmgc.eu.platform.sh`` and ``www-master-k4ywtmwigmmgc.eu.platform.sh``.

.. note::
  This will **not** work for an apex (or "naked") domain. In that case, you need to use a DNS provider that supports forwarding DNS queries (such as the ALIAS record on `Amazon's Route 53 <http://aws.amazon.com/route53/>`_, or the ANAME record on `DNS Made Easy <http://www.dnsmadeeasy.com/>`_). Many other providers also work arounds to accomplish this goal. The most common is to add a CNAME record for the ``www`` host on the domain and then use the DNS provider's redirection service to redirect the apex over to the ``www`` version of the domain. Check with your DNS provider to see how they support this.
