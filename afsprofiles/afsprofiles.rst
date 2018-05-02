User Profiles With AFS
----------------------

Overview
++++++++

In this exercise you will use Prism to deploy Acropolis File Services (AFS), a native, distributed file server solution for Nutanix clusters. The file server will be used to store user profiles and user data.

You will use Citrix Studio to configure and enforce policy for Citrix User Profile Management (UPM), a built-in profile management solution for XenDesktop. Citrix UPM can also be configured via Group Policy or .ini files within the desktop VM.

Deploying Acropolis File Services
+++++++++++++++++++++++++++++++++

Deploying AFS requires configuration of the cluster's **iSCSI Data Services IP** to provide block storage to AFS VMs via Acropolis Block Services (ABS). This virtual IP is used for target discovery, load balancing, and high availability, simplifying iSCSI configuration on clients. The IP has already been configured on this cluster, but can be viewed in **Prism >** :fa:`cog` **> Cluster Details**.

Additionally deploying AFS requires uploading an AFS binary to the cluster. Binaries can be downloaded directly from **Prism** on Internet-connected clusters, or downloaded from the `Nutanix Portal <https://portal.nutanix.com/#/page/releases/afsDetails>`_. The AFS binary has already been staged on this cluster, but can be viewed in **Prism >** :fa:`cog` **> Upgrade Software > File Server**.

In **Prism > File Server**, click **+ File Server**.

.. figure:: images/afsprofiles1.png

Fill out the following fields and click **Next**:

- **Name** - WorkshopAFS
- **Domain** - ntnxlab.local
- **File Server Size** 1 TiB

.. note::

  Clicking **Custom Configuration** will allow you to alter the scale up and scale out sizing of the AFS VMs based on User and Throughput targets, or by manually providing the desired File Server VM configuration. **Altering these values are not required for this exercise.**

.. todo stuff about client network - communicates with clients and AD/DNS

To configure the Client Network, fill out the following fields and click **Next**:

- **VLAN** - Primary - Managed
- **DNS Resolver IP** - 10.21.\ *XYZ*\ .40

.. figure:: images/afsprofiles2.png

.. note::

   Observe that IPs will be chosen for each of the File Server VMs from the DHCP pool. Selecting an Unmanaged VLAN requires you to manually provide static IP addresses for each of the File Server VMs.

   **As the File Server VMs will be joined to your domain, it is CRITICAL that the DNS Resolver IP point to the proper address.**

.. todo stuff about storage network, storage backplane between file server VMs and controller VMs via ABS

To configure the Storage Network, fill out the following fields and click **Next**:

- **VLAN** - Primary - Managed

.. note::

   Observe that IPs will be chosen for each of the File Server VMs from the DHCP pool. Selecting an Unmanaged VLAN requires you to manually provide static IP addresses for each of the File Server VMs. Also need info on why 4 IPs and not 3.

Fill out the following fields and click **Next**:

- Select **Use SMB Protocol**
- **Username** - Administrator
- **Password** - nutanix/4u
- Select **Make this user a File Server admin**

.. note::

  Selecting **Advanced Options** allows you to define a preferred domain controller and non-default Organizational Unit in Active Directory to place the File Server VMs.

  Nutanix AFS also provides NFSv4 exports capable of Unmanaged, LDAP, or Active Directory authentication.

Click **Create**.

.. note::

  Observe that a Nutanix Protection Domain will be automatically created to perform local snapshots of the AFS deployment. That same Protection Domain can be used to replicate AFS to a secondary Nutanix cluster for DR purposes.

Monitor deployment progress in **Prism > Tasks**.

.. figure:: images/afsprofiles3.png

Creating Home Share
+++++++++++++++++++

In **Prism > File Server > Share/Export**, click **+ Share/Export**.

Fill out the following fields and click **Next**:

- **Name** - home
- **Description** - User profiles and data
- **File Server** - WorkshopAFS
- **Protocol** - SMB
- **Share Type** - Home Directory and User Profiles

.. note::

  Selecting **Home Directory and User Profiles** optimizes the sharding of data for this use case by distributing users' home directories evenly across all VMs in the AFS cluster.

Fill out the following fields and click **Create**:

- Select **Enable Access Based Enumeration (ABE)**
- Select **Self Service Restore**

.. note::

  Access Based Enumeration (ABE) is a Microsoft Windows (SMB protocol) feature which allows the users to view only the files and folders to which they have read access when browsing content on the file server.

  Self Service Restore enabled support for Windows Previous Versions on an SMB share.

  Either of these features can be enabled/disabled on a per share basis.

Monitor share creation progress in **Prism > Tasks**.

.. figure:: images/afsprofiles4.png

In **Prism > File Server > File Server**, select **WorkshopAFS** and click **Protect**.

.. figure:: images/afsprofiles5.png

.. note::

  Note the default Self Service Restore schedules, this feature controls the snapshot schedule for Windows' Previous Versions functionality. Supporting Windows Previous Versions allows end users to roll back changes to files without engaging storage or backup administrators. Note these local snapshots do not protect the file server cluster from local failures and that replication of the entire file server cluster can be performed to remote Nutanix clusters.

In the **XD** VM console, validate you can access ``\\WorkshopAFS.ntnxlab.local\home\`` from File Explorer.

.. figure:: images/afsprofiles6.png

.. note::

  To learn more about AFS capabilities, including Quotas, Antivirus integration, monitoring, and more, see the `Acropolis File Services Guide <https://portal.nutanix.com/#/page/docs/details?targetId=Acropolis-File-Services-v30:Acropolis-File-Services-v30>`_ on the Nutanix Portal.

Configuring Share Permissions
+++++++++++++++++++++++++++++

.. todo about why we're setting these permissions, allowing all users to create a top level directory that they own for their profile

In the **XD** VM console, open ``\\WorkshopAFS.ntnxlab.local\`` in File Explorer.

Right-click **Home > Properties**.

Select the **Security** tab and click **Advanced**.

Select **Users (WorkshopAFS\\Users)** and click **Remove**.

Click **Add**.

Click **Select a principal** and specify **Everyone** in the **Object Name** field. Click **OK**.

Fill out the following fields and click **OK**:

- **Type** - Allow
- **Applies to** - This folder only
- Select **Read & execute**
- Select **List folder contents**
- Select **Read**
- Select **Write**

.. figure:: images/afsprofiles7.png

Click **OK > OK > OK**.

.. figure:: images/afsprofiles8.png

Configuring Citrix User Profile Management
++++++++++++++++++++++++++++++++++++++++++

.. todo on citrix UPM being installed as part of VDA, we are enabling the processing of those logons and telling it where to look for profiles

In **Citrix Studio > Policies**, right-click **Policies > Create Policy**.

.. figure:: images/afsprofiles9.png

Select **Profile Management > Basic Settings** from the **All Settings** drop down menu. Optionally you can filter for only policies supported on **7.15 Desktop OS** from the **All Versions** drop down menu.

.. figure:: images/afsprofiles10.png

 Search for **Enable Profile management** and click **Select**. Select **Enabled** and click **OK**.

.. figure:: images/afsprofiles11.png

Search for **Path to user store** and click **Select**. Select **Enabled** and specify ``\\WorkshopAFS\home\%USERNAME%\!CTX_OSNAME!!CTX_OSBITNESS!`` as the path. Click **OK**.

.. figure:: images/afsprofiles12.png

.. note::

  The specified path will not only create unique top level directories within the share for each user, but will also create a platform specific subdirectory for their profile to avoid incompatability issues, such as trying to apply a Windows 10 user profile to a Windows 2012 session.

Click **Next**.

Click **Assign** to the right of **Organizational Unit (OU)**.

Click **Browse** and select the **Non-PersistentDesktop** OU. Click **OK > OK**.

.. figure:: images/afsprofiles13.png

.. note::

  Studio offers many different means of applying policies. Across a more diverse environment it may make sense to configure UPM settings based on tags. If our computers weren't optimally organized in Active Directory, we could have also elected to assign this policy based on the Non-Persistent desktop Delivery Group.

Click **Next**.

Provide a friendly **Policy name** (e.g. **UPM**) and select **Enable policy**. Review your configuration and click **Finish**.

.. figure:: images/afsprofiles14.png

Testing Profiles and Folder Redirection
+++++++++++++++++++++++++++++++++++++++

Log in to Citrix StoreFront as **NTNXLAB\\basicuser01** and connect to a **Pooled Windows 10 Desktop**.

Make some simple changes such as adding files to your Documents folder and changing the desktop background. Note the hostname of the desktop to which you are connected.

.. figure:: images/afsprofiles15.png

Sign out of the **Pooled** desktop. Do not just close the Citrix Receiver session as the desktop will not be re-provisioned.

Again, log in to Citrix StoreFront as **NTNXLAB\\basicuser01** and connect to a **Pooled Windows 10 Desktop**. Note that your files and settings persist across sessions, despite the underlying desktop being freshly provisioned every time you log in.

Open ``**\\WorkshopAFS\home\basicuser01**`` in **File Explorer**. Drill down into the directory structure to find the data associated with your user profile.

Log in to Citrix StoreFront as **NTNXLAB\\basicuser02** and connect to a **Pooled Windows 10 Desktop**. Open ``\\WorkshopAFS\home\`` and note that you don't see or have access to **basicuser01**'s profile directory. Disable **Access Based Enumeration (ABE)** in **Prism > File Server > Share/Export > home > Update** and try again.

Create and save a text file in the **Documents** folder of your non-persistent virtual desktop. After ~1 hour, return to your virtual desktop, modify and save the document you previously created. Right-click the file and select **Restore previous versions**. Select an available previous version of the document and click **Open** to access the file.

.. figure:: images/afsprofiles16.png

Takeaways
+++++++++

- Nutanix provides native file services suitable for storing user profiles and data.

- AFS can be deployed on the same Nutanix cluster as your virtual desktops, resulting in better utilization of storage capacity and the elimination of an additional storage silo.

- Supporting mixed workloads (e.g. virtual desktops and file services) is further enhanced by Nutanix's ability to mix different node configurations within a single cluster, such as:

  - Mixing storage heavy and compute heavy nodes
  - Expanding a cluster with Storage Only nodes to increase storage capacity without incurring additional virtualization licensing costs
  - Mixing different generations of hardware (e.g. NX-3460-G6 + NX-6235-G5)
  - Mixing all flash nodes with hybrid nodes
  - Mixing NVIDIA GPU nodes with non-GPU nodes
