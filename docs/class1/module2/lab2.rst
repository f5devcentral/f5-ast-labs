.. _Configuring the F5 AST:

Lab 2 - Configuring, Updating, and Accessing the F5 AST
=======================================================

Environment Variables
---------------------

#. If your previously-opened *web shell* has closed or timed out, navigate to the **App Study Tool** component with the UDF lab, then select **Access** and **Web Shell**.

#. Change over to the ``application-study-tool`` repo's root directory:

    .. code-block:: console

        cd /home/ubuntu/application-study-tool/

#. Inspect the F5 AST environment variables by running the following commands:

    .. code-block:: console

        more .env
    .. code-block:: console
 
        more .env.device-secrets

    .. note:: The environment and configuration files contain documentation tidbits to aid the setup and tuning process

F5 AST Configuration Setting Files
----------------------------------

#. Browse the F5 AST default configuration by running the following command:

    .. code-block:: console

        more config/ast_defaults.yaml
    
    Here's an excerpt from the ``ast_defaults.yaml`` file:

    .. code-block:: console

        # These configs are applied to each entry in the bigip_receivers file
        # where they don't contain an equivalent / overriding entry.
        bigip_receiver_defaults:
            # The time to wait between metric collection runs
            collection_interval: 60s
            # The username to login to the device with
            username: admin
            # The password (not recommended) or a reference to an env variable (recommended, shown)
            # Below tells the collector to look for an environment variable named BIGIP_PASSWORD_1
            password: "${env:BIGIP_PASSWORD_1}"
            # The timeout field can be used to adjust the amount of time the collector will wait for a response
            # to BigIP iControl Rest requests. Larger boxes with more complex config may require setting this value
            # higher. Set for individual devices in bigip_receivers.yaml
            timeout: 10s
            # The data_types that should be enabled or disabled. DNS and GTM users can enable those modules
            # by setting the below to true. These will apply to all devices and may be better specified on the
            # per-reciever settings file below.
            data_types:
                f5.dns:
                enabled: true
                f5.gtm:
                enabled: true
            # The TLS settings to use. Either a CA file must be specified or insecure_skip_verify
            # set to true (not recommended)
            tls:
                insecure_skip_verify: true
                ca_file: ""

            # Set to true to enable periodic metric export to F5 DataFabric.
            # Requires adding your Sensor ID and secret token to the container environment (see .env-example).
            # Contact your F5 sales rep to obtain the ID / secret token.
            f5_data_export: false

    This file contains configuration parameters for both the F5 AST itself and the devices subject to data collection. As the name implies, default settings for device collection can be set here.

    Take a look at the value for ``password``. It's referencing an environment variable -- one which has been defined in the ``.env.device-secrets`` we reviewed in Step 7.
    
    .. note:: Default device settings can be overridden by individual device configurations in the ``config/bigip_receivers.yaml`` file.

    As mentioned in Step 4, we need to add a new BIG-IP instance for data scraping: ``APAC - bigip-01``. 

#. First, inspect the ``config/bigip_receivers.yaml`` file with the following command:

    .. code-block:: console

        more config/bigip_receivers.yaml

    Here's the configuration for one of the BIG-IPs:

    .. code-block:: console

        bigip/1:
            # Endpoint must be specified for each device
            # Set this to the management IP for the device. This must be
            # reachable from the Application Study Tool host.
            endpoint: https://10.1.1.5
            # Uncommenting any of the following lines will override the defaults in
            # ast_defaults.yaml bigip_receiver_defaults section.
            # username: SOME_OVERRIDE_ACCOUNT_NAME
            # password: "${SOME_OTHER_ENV_VAR_WITH_ANOTHER_PASSWORD}"
            # collection_interval: 30s
            # timeout: 20s
            # data_types:
            #   f5.dns:
            #     enabled: true
            #   f5.gtm:
            #     enabled: true
            # tls:
            #   insecure_skip_verify: true
            #   ca_file:``

    Notice how there are effectively only two lines of configuration for this BIG-IP, as its authentication settings are inherited from the ``ast_defaults.yaml`` file.

    In essence, that configuration boils down to this:

    .. code-block:: console

        bigip/1:
            endpoint: https://10.1.1.6

    Upon further examination of the configuration, the inline documentation makes understanding the settings and options an achievable task.

    Now that we've come familiar with the configuration settings, it's time to add a new BIG-IP section to the yaml file.

#. Open ``bigip_receivers.yaml`` for editing using ``vim`` (or another editor of your choosing):

    .. code-block:: console

        vim config/bigip_receivers.yaml

    While in vim, press ``Shift+G`` to take your cursor to the bottom line. Next, type ``ee``, moving your cursor to the end of the line.

    At the end of the line, press ``i`` to enter insert mode, then ``right-arrow``, followed by ``return``, taking you to a new line. Press ``backspace`` til the cursor is in the left-most position.

    Now, copy the following and paste it into the editor:

    .. code-block:: console

        bigip/4:
            endpoint: https://10.1.1.6

    To save your changes, press ``escape``, then type ``:wq`` and ``return``. You should see a message similar to the following upon successful save:

    .. code-block:: console

        "config/bigip_receivers.yaml" 82L, 2714B written

F5 AST Configuration Helper
---------------------------

Once the ``bigip_receivers.yaml`` file has been updated, you must run the configuration helper script. This processes the changes made and updates the OTel collector's embedded yaml configuration files, as we will soon see.

#. The following command must be run from the f5-application-study repo root directory, ``/home/ubuntu/application-study-tool``

   .. code-block:: console

      docker run --rm -it -w /app -v ${PWD}:/app --entrypoint /app/src/bin/init_entrypoint.sh python:3.12.6-slim-bookworm --generate-config

   Output ending with the following two lines indicates the configuration was successfully generated.

   .. code-block:: console

      2024-11-19 06:28:46,272 - INFO - Successfully wrote data to './services/otel_collector/pipelines.yaml'.
      2024-11-19 06:28:46,273 - INFO - Successfully wrote data to './services/otel_collector/receivers.yaml'.

Updating F5 AST
---------------

Let's check the release version of the repo by examining the ``docker-compose.yaml`` file, which resides in the repo root directory. The version running in this lab's corresponding UDF environment should, but may not always be up-to-date.

#. Review the ``docker-compose.yaml`` file:

   .. code-block:: console

      more docker-compose.yaml

   Press ``space`` until the entire file contents are revealed. Notice the ``otel-collector`` section and the ``image`` property therein.

   .. code-block:: console

      otel-collector:
         image: ghcr.io/f5devcentral/application-study-tool/otel_custom_collector:v0.7.0

   This particular output reveals v0.7.0 of the OTel Custom Collector. If that version is lower than what's listed on the `f5devcentral / application-study-tool Releases board <https://github.com/f5devcentral/application-study-tool/releases/tag/v0.7.0>`_, perform the following steps. Otherwise, you're ready and free to roll on to :ref:`Exploring the F5 AST Dashboards`.

#. Since local changes have been made to files which are actively tracked for changes in the repo, such as ``.env.device-secrets`` and ``config/bigip_receivers.yaml``, we must stash away those changes prior to performing a ``git pull``. Stashing simply sets them aside temporarily. We'll reincorporate them after pulling the latest code from GitHub.

   .. code-block:: console

      git stash

#. Pull new code from the GitHub repo:

   .. code-block:: console

      git pull origin main

#. Undo the ``git stash`` action, bringing our local changes back where they need to be:

   .. code-block:: console

      git stash pop

#. Run the F5 AST Configuration Helper:

   .. code-block:: console

      docker run --rm -it -w /app -v ${PWD}:/app --entrypoint /app/src/bin/init_entrypoint.sh python:3.12.6-slim-bookworm --generate-config

#. Restart the OTel Custom Collector container:

   .. code-block:: console

      docker container restart application-study-tool_otel-collector_1

That's it! The upgrade process should be seamless and good to go.

Accessing F5 AST
----------------

Here's where our boots hit the ground and the real adventure begins!

#. From within the UDF course deployment's **Super Jump Host** System, locate and select **ACCESS**, then **Firefox**.

    .. image:: images/udf_firefox_access.png
        :width: 800

#. Once the new browser tab has loaded, you will be presented with a nested Firefox browser that's running within the UDF lab. Click into the search/navigation bar and select the **Dashboards - Grafana**

    .. image:: images/udf_grafana_browser_link.png
        :width: 800

    As you can see, the F5 AST Grafana dashboard is available via the following URL in your lab environment:

    .. code-block:: console

        http://10.1.1.10:3000/dashboards

#. The **Dashboards** landing page presents users with a couple standalone dashboards and a few collections of dashboards, per the image below.

    .. image:: images/grafana_dashboards.png
        :width: 800

In the next module you will learn about all of the available pre-packaged dashboards. The door's open for you to step in and take a look around the F5 Application Study Tool!

Please select **Next** below and continue on to :ref:`Exploring the F5 AST Dashboards`