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

LTM HTTP Profile
----------------

.. image:: images/ltm_http_profile_dashboard.png
    :width: 800

LTM - DNS Profile
-----------------

.. image:: images/ltm_dns_profile_dashboard.png
    :width: 800