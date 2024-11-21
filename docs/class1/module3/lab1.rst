.. _BIG-IP - Fleet:

Section 1 - Dashboards: BIG-IP - Fleet
======================================

In this lab, we'll explore the Application Study Tool's BIG-IP Fleet dashboards:

* Fleet Inventory
* Device Utilization
* Virtual Servers
* SSL Certificate Status

.. note:: There is an `exhaustive list of metrics <https://github.com/f5devcentral/application-study-tool/blob/main/pages/components/otel_collector/receiver_metrics.md>`_ collected, some of which have been exposed through the pre-built dashboards you are about to explore.

    These dashboards provide views into fundamental metrics we have observed have value in real-world scenarios, based upon field experience. We have designed this tool in a way that facilitates and enables users to customize it to meet their unique needs. Grafana provides just that, and we encourage you to add it into your toolbelt.


Fleet Inventory
---------------

The **Fleet Inventory** dashboard is your one-stop shop for a list of all the F5 BIG-IPs in your estate from which F5 AST is collecting data.

Available Metrics include:

* Total Inventory by Software Version
* Total Inventory by Device Type
* Inventory Total by Device Type
* Online Inventory by Version
* Inventory Detail


.. image:: images/fleet_inventory_dashboard.png
    :width: 800

Device Utilization
------------------

The **Device Utilization** dashboard contains data pertaining to metrics such as *resource utilization* and *F5 BIG-IP configuration objects*.

Available Metrics include:

* System Resources

  * Average CPU Utilization

  * Average Memory Utilization

  * Disk Utilization

* Configuration Objects

  * Configured Virtual Servers

  * Configured Pools

  * Configured Pool Members

* Average Virtual Server Utilization

  * Current Client Side Connections

  * Client Side Total Bytes In/Out


.. image:: images/device_utilization_dashboard.png
    :width: 800

Virtual Servers
---------------

The **Virtual Servers** dashboard give a view into *availability* and *connection/data rate* stats.

From here, you can also see *if changes have occurred within the past 24 hours*, which answers one of the very first incident-triage questions... **what changed**?

Available Metrics include:

* Virtual Server Availability
* Is Virtual Server Available?
* Virtual Server Connection Rate
* Virtual Server Data Rate
* Fleet Virtual Server Details


.. image:: images/fleet_virtual_servers_dashboard.png
    :width: 800

SSL Certificate Status
----------------------

The **SSL Certificate Status** dashboard is both self-explanatory and extraordinarily helpful, especially when your F5 BIG-IP estate sprawls datacenters across the globe.

Available Metrics include:

- Summary

  - Certificate Type

  - Certs Expiring in less than 30 days

  - Certs Expiring in less than 90 days

  - Certs Expiring in less than 180 days

- Certificate Detail List


.. image:: images/ssl_certificate_status_dashboard.png
    :width: 800


Please select **Next** below and continue on to :ref:`BIG-IP - Device`.