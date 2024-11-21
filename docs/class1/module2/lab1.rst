.. _Accessing and Using the GitHub Repository:

Lab 1 - Accessing and Using the GitHub Repository
=================================================

F5 DevCentral Application Study Tool GitHub Repo
------------------------------------------------

Here it is, served to you on a nice, bountiful SaaS platter: |application-study-tool|

.. |application-study-tool| raw:: html

   <a href="https://github.com/f5devcentral/application-study-tool" target="_blank">f5devcentral / application-study-tool</a>

#. Navigate to the F5 Application Study Tool link above and observe the GitHub Repo main page

    .. image:: images/github_repo_home.png
        :width: 800

#. Scroll down the page and you will soon find the Readme.

    .. image:: images/github_repo_readme.png
        :width: 800

    .. note:: This is a fairly comprehensive guide for installing, managing, and updating the F5 AST. We won't follow it verbatim, but will touch on the fundamentals.
        
        We recommend you fully review and keep this and the `Docsite <https://f5devcentral.github.io/application-study-tool/>`_ in your bookmarks.

#. On the right side of the page are links to **Releases** and **Packages**

    .. image:: images/github_repo_releases_packages.png
        :width: 250

    Within a given **Release** you will find full details about it, as well as the source code (zip and tar.gz).

    The **Packages** section contains the ``otel_custom_collector`` image, which is referenced within the ``docker-compose.yaml`` file in the repo's root directory.

    .. note:: Building on the brief intro to managing containers with Docker, it is possible to run *multiple containers in conjunction with one another* through the use of **Docker Compose**.

        ``docker-compose.yaml`` contains the set of instructions that is interpreted by **Docker Compose**, which is responsible for orchestrating the *OTel Collector*, *Grafana*, and *Prometheus* containers.

#. Let's review the repo's installation instructions:

    .. code-block:: console

        # Clone the repo
        git clone https://github.com/f5devcentral/application-study-tool.git
        cd application-study-tool
        # Edit the following file with Grafana variables as required
        cp .env-example .env
        # Edit the following file with device secrets as required (see "Configure Device Secrets" below)
        cp .env.device-secrets-example .env.device-secrets
        # Edit the default settings for your environment as required
        # (see "Configure Default Device Settings" below)
        vi ./config/ast_defaults.yaml
        # Edit the config file with device / connection info
        # (see "Configure Devices To Scrape" below)
        vi ./config/bigip_receivers.yaml
        # Run the configuration generator
        docker run --rm -it -w /app -v ${PWD}:/app --entrypoint /app/src/bin/init_entrypoint.sh python:3.12.6-slim-bookworm --generate-config
        # Start the tool
        docker-compose up

    In this lab, the F5 Application Study Tool has already been installed, partially configured, and started.

    However, there's an F5 BIG-IP in our UDF lab environment which is not yet being monitored by F5 AST and needs to be added to the ``config/bigip_receivers.yaml`` file.

    With that said, we should review and familiarize ourselves with the current configuration and get our hands dirty by modifying it and restarting the OTel container using Docker.

    .. attention:: If you are interested in performing the full installation process, you may do so by stopping the currently-running instance of F5 AST and clone the repo into a different directory.
        
        From the new directory, you'll be working with a fresh installation. If you're unsure about how to do this, please ask an instructor or lab assistant for help.

        For the sake of this lab and showcasing traffic generation, we pre-staged F5 AST in the event students want to get up and running with accessing the Grafana dashboards right away.

Please select **Next** below and continue on to :ref:`Configuring the F5 AST`