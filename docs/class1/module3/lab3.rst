.. _Device/Profiles:

BIG-IP - Device/Profiles Dashboards
===================================

In this lab, we'll explore the Application Study Tool's BIG-IP Device/Profiles dashboards:

- LTM - HTTP Profile
- LTM - DNS Profile

.. note:: There is an `exhaustive list of metrics <https://github.com/f5devcentral/application-study-tool/blob/main/pages/components/otel_collector/receiver_metrics.md>`_ collected, some of which have been exposed through the pre-built dashboards you are about to explore.

    These dashboards provide views into fundamental metrics we have observed have value in real-world scenarios, based upon field experience. We have designed this tool in a way that facilitates and enables users to customize it to meet their unique needs. Grafana provides just that, and we encourage you to add it into your toolbelt.

#. From the **Grafana Dashboards** landing page, select **BIG-IP - Device/Profiles**

    .. image:: images/device_profiles_link.png
        :width: 500

#. You'll be presented with a list of available dashboards:

    .. image:: images/device_profiles_dashboards.png
        :width: 500

LTM - HTTP Profile
------------------

The **LTM - HTTP Profile** dashboard is where HTTP-specific data can be found on a per-profile basis.

Available Metrics include:

* Profile Overview

  * Profile Settings

* HTTP Request / Response

  * HTTP Request Rate

  * HTTP Response Rate by Status

  * HTTP Requests by Version (last hour)

  * HTTP Requests by Method

  * HTTP Responses by Version

* Virtual Server HTTP Request / Response

  * Virtual Server HTTP Request Rate by Method

  * Virtual Server HTTP Response Rate by Status


.. image:: images/ltm_http_profile_dashboard.png
    :width: 800

LTM - DNS Profile
-----------------

As you may have guessed, the **LTM - DNS Profile** dashboard is where DNS-specific data can be found, per-profile.

.. note:: One of the first things you'll notice about this dashboard is the presence of configuration instructions. Please take note of these and be sure your ``ast_defaults.yaml`` and ``bigip_receivers.yaml`` files are properly configured. Otherwise, stats will not be collected and displayed.

Available Metrics include:

* Profile Overview

  * Profile Settings

  * DNS Profile Request Rate

  * DNS Profile Response Rate

* DNS Request / Response Detail

  * DNS EDNS Request Rate

  * DNS EDNS Response Rate

  * DNS Request by Type

  * DNS Response by Type

  * DNS Response by Return Type

* DNS Zone NS Detail

  * Queries by Type

  * XFR Notify Rates

  * DNSX Query Rates

  * DNSX Response Rate

* DNS Resolver Cache Hit / Miss Rates

  * Client Cache Hits

  * Client Cache Misses

  * Client Hits Response Time

  * Client Misses Response Time

* DNS Resolver Cache Traffic Rates

  * DNS Profile Bit Rate

  * TCP Bit Rate

  * Packet Rate

* DNS Resolver Cache Message Rates

  * Message Hit Rate

  * Message Miss Rate

  * Message Eviction Rate

  * Message Modification Rate

* DNS Resolver Serer Query Rate

  * Server Query Rate

  * Server Response Rate

  * Server Queries


.. image:: images/ltm_dns_profile_dashboard.png
    :width: 800

This concludes :ref:`Introduction to F5 Application Study Tool`! We truly hope you've enjoyed the content and trust you'll check back soon for additional exciting content!