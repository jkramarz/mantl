Calico
======

.. versionadded:: 0.4

`Calico <http://www.projectcalico.org>`_ is used in the project to add the IP
per container functionality. Calico connects Docker containers through IP no matter
which worker node they are on. Calico uses :doc:`etcd` to distribute information
about workloads, endpoints, and policy to each worker node. Endpoints are
network interfaces associated with workloads. Calico is deployed in the Docker
container on each worker node and managed by systemd.

Calico is not enabled by default. In order to run Calico, you should make a
couple of changes to your ``mantl.yml``. You will need to add the ``etcd``
role into the ``roles`` section for ``all`` hosts:

.. code-block:: json

   - hosts: all
     ...
     roles:
       - common
       ...
       - etcd

And you need to add the ``calico`` role to the ``role=worker`` hosts:

.. code-block:: json

   - hosts: role=worker
     roles:
       ...
       - calico

Modes
^^^^^

Calico can run a public cloud environment that does not allow either L3 peering
or L2 connectivity between Calico hosts. Calico will then route traffic between
the Calico hosts using IP in IP mode. At this time, the full node-to-node BGP
mesh is supported and configured in OpenStack only. Other cloud environments
are set up with the IP in IP mode.

calicoctl
^^^^^^^^^

You can use the ``calicoctl`` command line tool to manually configure and start
the Calico services, interact with the etcd datastore, define and apply network
and security policies, and other.

Examples:

.. code-block:: shell

   calicoctl help
   calicoctl status
   calicoctl profile show --detailed
   calicoctl endpoint show --detailed
   calicoctl pool show

Logging
^^^^^^^

All components log to directories under ``/var/log/calico`` inside
the calico-docker container. By default this is mapped to
the ``/var/log/calico`` directory on the host. Files are automatically rotated,
and by default 10 files of 1MB each are kept.

Variables
---------

You can use these variables to customize your Calico installation. For more
information, refer to the :doc:`etcd` configuration.

.. data:: etcd_service_name

   Set the ``ETCD_AUTHORITY`` environment variable that is used by Calico Docker
   container and the CLI tool ``calicoctl``. The value of this variable is
   a Consul service that must be resolved through DNS

   Default: ``etcd.service.consul``

.. data:: etcd_client_port

   Port for etcd client communication

   Default: ``2379``

.. data:: calico_network

   Containers are assigned IPs from this network range

   Default: ``192.168.0.0/16``

.. data:: calico_profile

   Endpoints are added to this profile for interconnectivity

   Default: ``dev``
