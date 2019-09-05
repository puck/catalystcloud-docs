
*********************************************
Setting up access for non-cluster-admin users
*********************************************

For the purpose of this example we will use the following setup:

* cluster name - k8s-access-test
* cluster-admin user - clouduser
* assigned roles for cluster-admin user - Member
* non cluster-admin user - testuser-1
* assigned roles for non cluster-admin user - Auth Only & K8s Viewer


Currently only the cloud-admin user is capable of generating a the config file
necessary to be able to authenticate against a Kubernetes cluster launched
by the **Catalyst Cloud Container Infra service**. In a effort to simplify
cluster operations it is now possible for the ``cluster-admin`` to generate a
generic cluster config file that can be shared to all members of the team that
require access to a given cluster.

By itself the cluster config file will not provide access to the cluster it
was generated for. It is also necessary for the user wishing to interact with
the cluster to have one of the Kubernetes specific access roles assigned to
them.

The **Kubernetes specific roles** currently available in the Catalyst Cloud
are:

* ``k8s-admin`` role can create/update/delete Kubernetes cluster, can also
  associate roles to other normal users within the tenant.
* ``k8s-developer`` can create/update/delete/watch Kubernetes cluster
  resources.
* ``k8s-viewer`` can only have read access to Kubernetes cluster resources.

These roles can be added to an existing user through the **Management ->
Access Control -> Project Users** page by anyone who has the Project Admin or
Project Moderator roles assigned to their account.

// TODO - add image from user panel

Generating the cluster configuration file
=========================================

As the cluster-admin user we need to generate the token based cluster
configuration file to pass along to our non-admin users. To do this run the
following command.

..code-block:: bash

  $ openstack coe cluster config k8s-access-test --use-keystone

The output of this will be a file named ``config`` in the current working
directory. A copy of this will need to be made available to any user that
requires access to this cluster.

*****************************************
Accessing the cluster as a non-admin user
*****************************************

Source the user's openrc file and confirm that they can authenticate
successfully by retrieving an authentication token.

.. code-block:: bash

  $ openstack token issue
  +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Field      | Value                                                                                                                                                                                   |
  +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | expires    | 2019-08-27T10:52:57+0000                                                                                                                                                                |
  | id         | gAAAAABdZEapxNEKv3Ryw3PUTv2vIVZUEZafgXcH4YFS9bVt1Rdt6jkMRRnhwodfeiKNU0kXPs1tA1xuO1RsEfIlFJ2Dt0Zh5haHkENnT94TGDOzWnh01okvDCVHj3zURJ6u2vp5MDETpu1tu_tu52uTVdLe7rdWDKrV-lmOE0yrYeNpyb8mis8 |
  | project_id | eac679e4896146e6827ce29d755fe289                                                                                                                                                        |
  | user_id    | 11d1cb41f05140ebadcec49b9a67a2d7                                                                                                                                                        |
  +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Once we have copied the config generated in the previous step to the non-admin
users home directory we need to create an environment variable to let
``kubectl`` know where to find it's configuration file.

.. code-block:: bash

  $ export KUBECONFIG='/home/testuser-1/config'

That is all the setup that is required to enable access to the K8saccess-test
cluster for testuser-1. They should now be able to perform any ``GET`` or
``LIST`` operation on resources in the default namespace.

This process will be identical for users that have been assigned the other
Kubernetes specific roles with the only difference being what rights the user
gets granted.
