.. _Installing the F5 AST:

Lab 2 - Full Installation of the F5 AST
=======================================

Within this lab, you will undertake the task of performing a full installation of the F5 Application Study Tool onto an Ubuntu host.

.. attention:: Some platforms may require the ``Shift`` key in conjunction with standard copy/paste key combinations when interacting with the **Web Shell**

   For example, use ``Shift + Ctrl + v`` to paste instead of ``Ctrl + v``

Pre-requisites Installation
---------------------------

#. Navigate to the **App Services & Traffic Generation** component with the UDF lab, then select **Access** and **Web Shell**.

#. Switch to the `ubuntu` user

    .. code-block:: console

        su - ubuntu

#. Install Git Client (Ubuntu)

    .. code-block:: console

        sudo apt install git-all

    .. note:: For more options, there are instructions for installing on several different Unix distributions on the Git website, at https://git-scm.com/download/linux

#. Install Docker (Ubuntu)

    #. Set up Docker's ``apt`` repository by first adding Docker's official GPG key, then adding the repository to Apt sources:

        .. code-block:: console

            sudo apt-get update
            sudo apt-get install ca-certificates curl
            sudo install -m 0755 -d /etc/apt/keyrings
            sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
            sudo chmod a+r /etc/apt/keyrings/docker.asc

            echo \
            "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
            $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
            sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
            sudo apt-get update

    #. Install the Docker packages

        .. code-block:: console

            sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

F5 Application Study Tool Installation and Setup
------------------------------------------------

#. Ensure you're in the `ubuntu` user home directory

    .. code-block:: console

        cd && pwd

#. Clone the F5 Application Study Tool repo and enter the directory

    .. code-block:: console

        git clone https://github.com/f5devcentral/application-study-tool.git && cd application-study-tool

#. Create localized copies of the environment variables and ``Device Secrets`` files.

    .. code-block:: console

        cp .env-example .env
       
    .. code-block:: console

        cp .env.device-secrets-example .env.device-secrets

#. Inspect the F5 AST environment variables by running the following commands:

    .. code-block:: console

        more .env

    .. code-block:: console
 
        more .env.device-secrets

    .. note:: The environment and configuration files contain documentation tidbits to aid the setup and tuning process

#. Update the ``Device Secrets`` file via ``vim`` (or another editor of your choosing):

    .. code-block:: console

        vim .env.device-secrets

    While in vim, press ``Shift+G`` to take your cursor to the bottom line and type ``dd`` to delete the line for ``BIGIP_PASSWORD_2``. Next, type ``e`` until your cursor reaches the end of the line.

    At the end of the line, press ``i`` to enter insert mode, then ``right-arrow``. Press ``backspace`` until ``A_SECRET_PASSWORD`` has been removed.

    Now, copy and paste the following password into the editor:

    .. code-block:: console

        f5Twister!

    The contents of the file should now look like this:

    .. code-block:: console

        # Names here are arbitrary, but must match values in big-ips.json.
        # Passwords can be referenced by many devices (you do not need a unique variable for each device).
        BIGIP_PASSWORD_1=f5Twister!

    To save your changes, press ``escape``, then type ``:wq`` and ``return``. You should see a message similar to the following upon successful save:

    .. code-block:: console

        ".env.device-secrets" 3L, 194B written

#. Examine the F5 AST default configuration by running the following command:

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
          # The timeout field can be used to adjust the maximum amount of time the collector will wait for a response
          # to BigIP iControl Rest requests. Larger boxes with more complex config may require setting this value
          # higher.
          # This value should be less than or equal to the collection_interval. Any requests that haven't completed
          # before this timer expires (starting at the beginning of the collection interval) will be cancelled.
          # You can set this for individual devices in bigip_receivers.yaml.
          timeout: 60s
          # The data_types that should be enabled or disabled. Default-disabled module users can enable those modules
          # by setting the below to true. These will apply to all devices and may be better specified on the
          # per-reciever settings file.
          data_types:
            f5.apm:
              enabled: false
            f5.cgnat:
              enabled: false
            f5.dns:
              enabled: false
            f5.dos:
              enabled: false
            f5.firewall:
              enabled: false
            f5.gtm:
              enabled: false
          # The TLS settings to use. Either a CA file must be specified or insecure_skip_verify
          # set to true (not recommended)
          tls:
            insecure_skip_verify: false
            ca_file: ""

        # Set to true to enable periodic metric export to F5 DataFabric.
        # Requires adding your Sensor ID and secret token to the container environment (see .env-example).
        # Contact your F5 sales rep to obtain the ID / secret token.
        f5_data_export: false

    This file contains configuration parameters for both the F5 AST itself and the devices subject to data collection. As the name implies, default settings for device collection can be set here.

    Take a look at the value for ``password``. It's referencing an environment variable -- one which has been defined in the ``.env.device-secrets`` we reviewed in Step 5.
    
    .. note:: Default device settings can be overridden by individual device configurations in the ``config/bigip_receivers.yaml`` file.

#. Edit the ``AST Defaults`` configuration file.

    .. code-block:: console

        sudo vim config/ast_defaults.yaml

    The one property we're interested in changing is **insecure_skip_verify**, nested within the **tls** section. Setting this value to **true** will allow self-signed BIG-IP device certificates to be ignored and allowed.

    Using the arrow keys, navigate to the end of the **insecure_skip_verify** line, press ``i`` to enter insert mode, then ``right-arrow``. Press ``backspace`` until ``false`` has been removed and type the word ``true``.

    To save your changes, press ``escape``, then type ``:wq`` and ``return``. You should see a message similar to the following upon successful save:

    .. code-block:: console

        "config/ast_defaults.yaml" 61L, 2903B written

#. Edit the ``BIG-IP Receivers`` configuration file

    .. code-block:: console

        sudo vim config/bigip_receivers.yaml

    For the sake of simplicity, we're going to remove all contents of the file, then paste in the new configuration.

    In order to preserve the formatting of multi-line copy/paste, we need to **set paste** within vim by typing ``:set paste`` and pressing ``return``. It will appear as though nothing has occurred. This is expected behavior.

    To remove the existing configuration, type ``:%d`` and press ``return``. This will delete all lines.

    Next, ``i`` to enter insert mode and copy/paste the following into the editor:

    .. code-block:: console

        # Your bigip targets
        # Values not explicitly configured here inherit values in
        # the ast_defaults.yaml bigip_receiver_defaults section.
        # Each entry must have a unique name, starting with bigip/
        # (e.g. bigip/1, bigip/2)
        bigip/1:
          endpoint: https://10.1.1.5
          data_types:
            f5.apm:
              enabled: false
            f5.cgnat:
              enabled: false
            f5.dns:
              enabled: true
            f5.dos:
              enabled: true
            f5.firewall:
              enabled: true
            f5.gtm:
              enabled: true
        bigip/2:
          endpoint: https://10.1.1.6
          data_types:
            f5.apm:
              enabled: false
            f5.cgnat:
              enabled: false
            f5.dns:
              enabled: false
            f5.dos:
              enabled: false
            f5.firewall:
              enabled: true
            f5.gtm:
              enabled: false
        bigip/3:
          endpoint: https://10.1.1.7
          data_types:
            f5.apm:
              enabled: false
            f5.cgnat:
              enabled: false
            f5.dns:
              enabled: false
            f5.dos:
              enabled: false
            f5.firewall:
              enabled: false
            f5.gtm:
              enabled: false

    To save your changes, press ``escape``, then type ``:wq`` and ``return``. You should see a message similar to the following upon successful save:

    .. code-block:: console

        "config/bigip_receivers.yaml" 50L, 986B written

#.  Run the Configuration Generator

    The Configuration Generator will collect the contents of the files we previously viewed/edited and prepare them for the AST collector container.

    .. code-block:: console

        sudo docker run --rm -it -w /app -v ${PWD}:/app --entrypoint /app/src/bin/init_entrypoint.sh python:3.12.6-slim-bookworm --generate-config

    You should see a slew of output resembling the following:

    .. code-block:: console

        Collecting PyYAML==6.0.2
        Downloading PyYAML-6.0.2-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (2.1 kB)
        Downloading PyYAML-6.0.2-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (767 kB)
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 767.5/767.5 kB 9.2 MB/s eta 0:00:00
        Installing collected packages: PyYAML
        Successfully installed PyYAML-6.0.2
        WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager, possibly rendering your system unusable.It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv. Use the --root-user-action option if you know what you are doing and want to suppress this warning.

        [notice] A new release of pip is available: 24.2 -> 25.0
        [notice] To update, run: pip install --upgrade pip
        2025-02-07 20:19:12,657 - INFO - Generating configs from  ./config/ast_defaults.yaml and ./config/bigip_receivers.yaml...
        2025-02-07 20:19:12,657 - INFO - Loading AST Default Settings in ./config/ast_defaults.yaml...
        2025-02-07 20:19:12,666 - INFO - Successfully loaded './config/ast_defaults.yaml'.
        2025-02-07 20:19:12,666 - INFO - Loading Per-Receiver (BigIP) Settings in ./config/bigip_receivers.yaml...
        2025-02-07 20:19:12,669 - INFO - Successfully loaded './config/bigip_receivers.yaml'.
        2025-02-07 20:19:12,669 - INFO - Generating receiver configs...
        2025-02-07 20:19:12,669 - INFO - Generating pipeline configs...
        2025-02-07 20:19:12,669 - WARNING - The f5_data_export=true and f5_pipeline_default fields are required to export metrics periodically to F5. Contact your F5 Sales Rep to provision a Sensor ID and Access Token.
        2025-02-07 20:19:12,670 - INFO - Built the following pipeline file:

        metrics/local:
        exporters:
        - otlphttp/metrics-local
        - debug/bigip
        processors:
        - batch/local
        receivers:
        - bigip/1
        - bigip/2
        - bigip/3

        2025-02-07 20:19:12,674 - INFO - Built the following receiver file:

        bigip/1:
          collection_interval: 60s
          data_types:
            enabled: true
            f5.dns: null
            f5.gtm: null
          endpoint: https://10.1.1.5
          password: ${env:BIGIP_PASSWORD_1}
          timeout: 10s
          tls:
            ca_file: ''
            insecure_skip_verify: true
          username: admin
        bigip/2:
          collection_interval: 60s
          data_types:
            f5.dns:
              enabled: false
            f5.gtm:
              enabled: false
          endpoint: https://10.1.1.6
          password: ${env:BIGIP_PASSWORD_1}
          timeout: 10s
          tls:
            ca_file: ''
            insecure_skip_verify: true
          username: admin
        bigip/3:
          collection_interval: 60s
          data_types:
            f5.dns:
              enabled: false
            f5.gtm:
              enabled: false
          endpoint: https://10.1.1.7
          password: ${env:BIGIP_PASSWORD_1}
          timeout: 10s
          tls:
            ca_file: ''
            insecure_skip_verify: true
          username: admin

        2025-02-07 20:19:12,675 - INFO - Successfully wrote data to './services/otel_collector/pipelines.yaml'.
        2025-02-07 20:19:12,679 - INFO - Successfully wrote data to './services/otel_collector/receivers.yaml'.

    The last two lines indicate success. If you're in an instructor-led class and do not see the success messages, please alert the lab assistants and request help.

#.  Start the F5 Application Study Tool

    .. code-block:: console

        sudo docker compose up -d

    .. note:: Depending on the version of Docker installed, you may need to run **sudo docker-compose up -d**. The command above is tailored to the version of Docker installed within the UDF lab at the time this guide was authored.

    You should see output similar to the following:

    .. code-block:: console

        Creating network "application-study-tool_7lc_network" with the default driver
        Creating grafana                                 ... done
        Creating application-study-tool_otel-collector_1 ... done
        Creating prometheus                              ... done


.. _`Accessing F5 AST`:

Accessing F5 AST
----------------

Here's where our boots hit the ground and the real adventure begins!

#. From within the UDF course deployment's **App Services & Traffic Generation** System, locate and select **ACCESS**, then **Self-Installed Grafana**.

    .. image:: images/udf_selfinstall_grafana_access.png
        :width: 800

#. Once the new browser tab has loaded, you will be presented with the self-installed AST Grafana login. Enter the following credentials and select **Log in**.

    Username

    .. code-block:: console

        admin

    Password

    .. code-block:: console

        admin

    .. image:: images/grafana_login.png
        :width: 800

    Although you won't see the internal, local URL, the self-installed F5 AST Grafana dashboard is exposed via:

    .. code-block:: console

        http://10.1.1.9:3000/dashboards

#. Next, you'll be presented with the Grafana homepage. From here, select the menu button next to **Home**, then **Dashboards**.

    .. image:: images/grafana_home.png
        :width: 800

    .. image:: images/grafana_access_dashboards.png
        :width: 400

#. The **Dashboards** landing page presents users with a couple standalone dashboards and a few collections of dashboards, per the image below.

    .. image:: images/grafana_dashboards.png
        :width: 800

In :ref:`Exploring the F5 AST Dashboards`, you will learn about all of the available pre-packaged dashboards. The door's open for you to step in and take a look around the F5 Application Study Tool!

Selecting **Next** below will take you to the :ref:`Configuring the F5 AST` lab, which you may want to skip, since you've already walked through the full, clean installation and setup process.