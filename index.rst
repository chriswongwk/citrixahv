.. title:: Citrix XenDesktop

.. toctree::
  :maxdepth: 2
  :caption: Labs
  :name: _labs
  :hidden:

  installxd/installxd
  w10goldimage/w10goldimage
  pdesktops/pdesktops
  npdesktops/npdesktops
  afsprofiles/afsprofiles
  updatecatalog/updatecatalog

.. toctree::
  :maxdepth: 2
  :caption: Optional Labs
  :name: _optional_labs
  :hidden:

  xenapp/xenapp
  ssflr/ssflr
  resiliency/resiliency

.. toctree::
  :maxdepth: 1
  :caption: Appendix
  :name: _appendix
  :hidden:

..  appendix/vdiprimer
..  appendix/glossary

.. _getting_started:

---------------
Getting Started
---------------

.. raw:: html

  <strong><font color="red">Please do not start any labs before reviewing the information on this page.</font></strong><br><br>

Nutanix Workshops provides hands-on guides for understanding and implementing a variety of solutions on top of the leading Enterprise Cloud Platform.

In this Workshop you will install and configure a XenDesktop 7.15 LTSR environment, deploy virtual desktops and applications quickly & easily on Nutanix AHV, deploy native Nutanix file services and configure user profles, perform Day 2 operations, and more! After completing the Workshop you should have an understanding of the many advantages that Nutanix offers for running XenDesktop environments.

What's New
++++++++++

**Coming Soon!**

- Citrix PVS/Nutanix AHV integration
- Remote access with Netscaler VPX running on Nutanix AHV

**April 27th, 2018 Updates**

- Workshop updated for AOS 5.6, AHV 20170830.115, and AFS 3.0.0.1

Environment Details
+++++++++++++++++++

Nutanix Workshops are intended to be run in the Nutanix Hosted POC environment. Your cluster will be provisioned with all necessary images, networks, and VMs required to complete the exercises.

Networking
..........

Hosted POC clusters follow a standard naming convention, where the numerical suffix corresponds to the subnet for that cluster:

- **Cluster Name** - POC\ *XYZ*
- **Subnet** - 10.21.\ *XYZ*\ .0
- **Cluster IP** - 10.21.\ *XYZ*\ .37

For example:

- **Cluster Name** - POC055
- **Subnet** - 10.21.55.0
- **Cluster IP** - 10.21.55.37

Throughout the Workshop there are multiple instances where you will need to substitue *XYZ* with the correct octet for your subnet, for example:

.. list-table::
   :widths: 25 75
   :header-rows: 1

   * - IP Address
     - Description
   * - 10.21.\ *XYZ*\ .37
     - Nutanix Cluster Virtual IP
   * - 10.21.\ *XYZ*\ .39
     - **PC** VM IP, Prism Central
   * - 10.21.\ *XYZ*\ .40
     - **DC** VM IP, NTNXLAB.local Domain Controller

Each cluster is configured with 2 VLANs which can be used for VMs:

.. list-table::
  :widths: 25 25 10 40
  :header-rows: 1

  * - Network Name
    - Address
    - VLAN
    - DHCP Scope
  * - Primary
    - 10.21.\ *XYZ*\ .1/25
    - 0
    - 10.21.\ *XYZ*\ .50-10.21.\ *XYZ*\ .124
  * - Secondary
    - 10.21.\ *XYZ*\ .129/25
    - *XYZ1*
    - 10.21.\ *XYZ*\ .132-10.21.\ *XYZ*\ .253

Credentials
...........

.. note::

  The *<Cluster Password>* is unique to each cluster and will be provided by the leader of the Workshop.

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - Credential
     - Username
     - Password
   * - Prism Element
     - admin
     - *<Cluster Password>*
   * - Prism Central
     - admin
     - *<Cluster Password>*
   * - Controller VM
     - nutanix
     - *<Cluster Password>*
   * - Prism Central VM
     - nutanix
     - *<Cluster Password>*

Each cluster has a dedicated domain controller VM, **DC**, responsible for providing AD services for the **NTNXLAB.local** domain. The domain is populated with the following Users and Groups:

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - Group
     - Username(s)
     - Password
   * - Administrators
     - Administrator
     - nutanix/4u
   * - SSP Admins
     - adminuser01-adminuser25
     - nutanix/4u
   * - SSP Developers
     - devuser01-devuser25
     - nutanix/4u
   * - SSP Power Users
     - poweruser01-poweruser25
     - nutanix/4u
   * - SSP Basic Users
     - basicuser01-basicuser25
     - nutanix/4u

Access Instructions
+++++++++++++++++++

The Nutanix Hosted POC environment can be accessed a number of different ways:

Citrix XenDesktop
.................

https://citrixready.nutanix.com - *Accessible via the Citrix Receiver client or HTML5*

**Nutanix Employees** - Use your NUTANIXDC credentials

**Non-Employees** - **Username:** POC\ *XYZ*\ -User01 (up to POC\ *XYZ*\ -User20), **Password:** *<Cluster Password>*

Employee Pulse Secure VPN
..........................

https://sslvpn.nutanix.com - Use your CORP credentials

Non-Employee Pulse Secure VPN
..............................

https://lab-vpn.nutanix.com - **Username:** POC\ *XYZ*\ -User01 (up to POC\ *XYZ*\ -User20), **Password:** *<Cluster Password>*

Under **Client Application Sessions**, click **Start** to the right of **Pulse Secure** to download the client.

Install and open **Pulse Secure**.

Add a connection:

- **Type** - Policy Secure (UAC) or Connection Server
- **Name** - HPOC VPN
- **Server URL** - lab-vpn.nutanix.com
