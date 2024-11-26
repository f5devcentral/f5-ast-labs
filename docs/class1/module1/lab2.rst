.. _A High-Level Overview of Containerization:

Section 2 - A High-Level Overview of Containerization
=====================================================

What is a Container?
--------------------

The tech industry has come a long way from the days of a single application hosted on a single physical server. We have lived (and still live) the experience of slicing and dicing physical server resources into virtual machines. Furthermore, an even finer layer of abstraction evolved and emerged: **containers**.

`According to Docker <https://www.docker.com/resources/what-container/>`_, a container is defined as:

   *"... a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings."*

Containers have revolutionized what it means and entails to deploy and manage software, whether it's a single container or a Kubernetes environment consisting of tens, hundreds, or even thousands of interconnected containers.

.. Note:: You may have heard the term **micro-services** floating around in recent years. Containerization is a foundational key to the success and proliferation of micro-services architectures.

At F5, our home is in the heart of application delivery and security, which exposes us to nearly every form of application deployment. We aim to not only stay aligned with modern application infrastructure practices, but also help forge the road ahead and be forward-thinking. Because of this, we are continually evaluating how we design, produce, and deploy our own software.

The F5 AST marks an exciting moment in our journey to ease the burden of observing, analyzing, managing, and delivering your most valuable and critical applications and services.

As you will see in the following module, the installation and configuration process is lightweight and quick!

But first, let's take a few moments to briefly get our feet wet with Docker and examine the UDF lab environment.

   #. Navigate to the **App Study Tool** component with the UDF lab, then select **Access** and **Web Shell**.

   #. Switch over to the ``ubuntu`` user:

      .. code-block:: console

         su - ubuntu

   #. Once the new tab appears and you're on the terminal, view the Docker version by entering the following command:

      .. code-block:: console

         sudo docker -v

   #. Curious which containers Docker are running? Perform this command:

      .. code-block:: console

         sudo docker ps

      .. note:: *docker ps* instructs Docker to list all **running** containers. Adding the *-a* argument will include containers not currently running.
         
         This can be helpful as an initial step when troubleshooting container issues, as you can see when each container was created and its status.

   #. To view all images available for Docker to run as containers, enter the following command:

      .. code-block:: console

         sudo docker image ls

   #. Ever wonder how to inspect Docker container logs? Run the following:

      .. code-block:: console

         sudo docker container logs application-study-tool_otel-collector_1

      .. note:: Reflect back upon the output of ``docker ps`` and you will notice a line item in the table with a name of ``application-study-tool_otel-collector_1``. This name can be referenced when interogating containers via the ``docker container <command> <container_name>`` sequence.


We highly encourage you to learn more about containerization and recommend `Docker for Beginners <https://docker-curriculum.com/>`_, as it is a well-written, comprehensive guide which builds a solid foundation for further growth.

.. attention:: The `Webapps with Docker <https://docker-curriculum.com/#webapps-with-docker>`_ section is a fantastic way to get your hands dirty and start seeing how containers work themselves into modern application delivery. Just be sure not to *excitedly* skim past the **Introduction**, **Getting Started**, and **Hello World** sections if you're unfamiliar with the basics of containers!

Please select **Next** below and continue on to :ref:`Installing and Configuring the F5 AST`