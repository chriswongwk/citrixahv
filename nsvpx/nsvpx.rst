*Optional Lab* - Remote Access With NetScaler VPX
-------------------------------------------------

Overview
++++++++

In this exercise you will deploy a NetScaler virtual appliance and configure NetScaler Gateway to provide remote access capabilities for your XenDesktop Site. Configuring remote access for a Citrix environment requires valid SSL certificates. To support this requirement you will configure the NetScaler VPX to act as a Root Certificate Authority, create and sign a server SSL certificate for the appliance, and configure your client to trust the Root certificate.

Deploying NetScaler Virtual Appliance
+++++++++++++++++++++++++++++++++++++

Obtain the latest NetScaler Gateway VPX for KVM build from https://www.citrix.com/downloads.

.. note:: As of writing the current version is 12.0-53.13.

Obtain a 90 day trial license from https://www.citrix.com/lp/try/netscaler-vpx-platinum.html.

Extract the contents of NSVPX*.tgz using a tool such as 7zip, WinRar, or WinZip.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/1.png

In **Prism**, click the **Settings** icon and select **Image Configuration**.

Click **Upload Image** and fill out the following fields:

- **Name** - *NetScaler VPX 12*
- **Annotation** - This is an optional field that is helpful for designating additional information about an image, such as version or filename
- **Image Type** - *DISK*
- **Storage Container** - *Default*
- **Image Source** - Upload **NSVPX-KVM-\*.qcow2**

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/2.png

Click **Save** and wait for the image to complete being uploaded.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/3.png

Click **Close**.

Using an SSH client, execute the following:

.. code:: bash

  > ssh nutanix@<NUTANIX-CLUSTER-IP>
  > acli
  <acropolis> vm.create NSVPX num_vcpus=2 num_cores_per_vcpu=1 memory=4G
  <acropolis> vm.disk_create NSVPX bus=ide clone_from_image=<NetScaler VPX Disk Image Name>
  <acropolis> vm.nic_create NSVPX network=<IPAM Network Name>
  <acropolis> vm.serial_port_create NSVPX type=kServer index=0
  <acropolis> vm.on NSVPX

In **Prism > VM > Table**, select the **NSVPX** VM and click **Launch Console**.

In the **NSVPX** VM console, log in with the default credentials (*nsroot*/*nsroot*)

Enter the initial configuration wizard:

.. code::

  config ns

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/4.png

Enter **1** and specify the NSVPX Management IP address from your Environment Details Worksheet.

Press **Return** and enter the netmask. Press **Return**.

Enter **7** and press **Return** to apply the changes.

Specify **Yes** to saving the changes and **No** to rebooting the VM.

Configure the default gateway and restart NSVPX:

.. code::

  > add route 0.0.0.0 0.0.0.0 <IPAM Network Gateway>
  > save ns config
  > reboot

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/5.png

Open \https://<*NSVPX-IP*> in your browser to access the **NetScaler web console**. Log in as **nsroot**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/6.png

Click **Enable** or **Skip**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/7.png

Click **Subnet IP Address**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/8.png

Enter your Subnet IP Address and Netmask according to your Environment Details Worksheet. The Subnet IP is what the NetScaler uses to communicate with other backend services, such as the StoreFront server. In a production environment the Subnet IP would likely be on a separate interface/subnet than the Management IP.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/9.png

Click **Host Name, DNS IP Address, and Time Zone**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/10.png

Fill out the following fields and click **Done**:

- **Host Name** - *NSVPX*
- **DNS IP Address** - DC IP address from your Environment Details Worksheet
- **Time Zone** - Your local timezone

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/11.png

Click **Yes** to allow the VM to reboot.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/12.png

Locate your e-mail from **Citrix Trials** for **Your Trial License for NetScaler VPX Platinum Edition**. Copy the trial license code to your clipboard.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/13.png

After the VM reboots you will be returned to the login page. Log in as **nsroot**.

Click **Licenses**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/14.png

Click **Add New License** and select **Use License Access Code**. Paste your trial license code in the **License Access Code** field and click **Get Licenses**.

.. note:: If your environment does not have Internet access, follow the on-screen instructions for manually downloading licenses from http://www.mycitrix.com.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/15.png

Select the license and enter **1** in the **Allocate** field. Click **Download**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/16.png

Click **Reboot > Yes** to complete the license installation.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/17.png

After the VM reboots you will be returned to the login page. Log in as **nsroot**.

Configuring Root Certificate Authority
++++++++++++++++++++++++++++++++++++++

Select **Traffic Management > SSL** and click **Root-CA Certificate Wizard**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/18.png

Fill out the following fields and click **Create**:

- Select **RSA**
- **Key Filename** - *root.key*
- **Key Size** - *2048*
- **Public Exponent Value** - *3*
- **PEM Encoding Algorithm** - *DES3*
- **PEM Passphrase** - *nutanix/4u*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/19.png

Fill out the following fields and click **Create**:

- **Request File Name** - *root.csr*
- **PEM Passphrase** - *nutanix/4u*
- **Country** - *United States*
- **State** - *CA*
- **Organization Name** - *NutanixWorkshop*
- **Common Name** - *NutanixWorkshop-Root-CA*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/20.png

Fill out the following fields and click **Create**:

- **Certificate File Name** - *root.cer*
- **PEM Passphrase** - *nutanix/4u*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/21.png

Enter *root* as the **Certificate Key Pair Name** and click **Create**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/22.png

Click **Done**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/23.png

Configuring Server SSL Certificate
++++++++++++++++++++++++++++++++++++++

Select **Traffic Management > SSL** and click **Server Certificate Wizard**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/24.png

Fill out the following fields and click **Create**:

- Select **RSA**
- **Key Filename** - *mydesktop.key*
- **Key Size** - *2048*
- **Public Exponent Value** - *3*
- **PEM Encoding Algorithm** - *DES3*
- **PEM Passphrase** - *nutanix/4u*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/25.png

Fill out the following fields and click **Create**:

- **Request File Name** - *mydesktop.csr*
- **PEM Passphrase** - *nutanix/4u*
- **Country** - *United States*
- **State** - *CA*
- **Organization Name** - *NutanixWorkshop*
- **Common Name** - *mydesktop.ntnx.local*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/26.png

Fill out the following fields and click **Create**:

- **Certificate File Name** - *mydesktop.cer*
- **CA Certificate File Name** - *root.cer*
- **CA Key File Name** - *root.key*
- **PEM Passphrase** - *nutanix/4u*
- **CA Serial File Number** - *CAserials*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/27.png

Fill out the following fields and click **Create**:

- **Certificate Key Pair Name** - *mydesktop*
- **Password** - *nutanix/4u*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/28.png

Click **Done**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/29.png

Configuring NetScaler for XenDesktop
++++++++++++++++++++++++++++++++++++

Select **XenApp and XenDesktop** from the menu and click **Get Started**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/30.png

Select **StoreFront** and click **Continue**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/31.png

Fill out the following fields and click **Continue**:

- **Gateway FQDN** - *mydesktop.ntnx.local*
-	**Gateway IP Address** - Refer to your Environment Details Worksheet
- **Port** - *443*

Note that the Gateway FQDN corresponds to the Common Name of our SSL certificate. The Gateway IP Address (also referred to as the Virtual IP Address or VIP) is the IP address used to communicate with external networks. In a production environment the VIP would be on a separate interface/subnet, typically in the DMZ.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/32.png

Select **mydesktop** from the **Server Certificate** drop down menu and click **Continue**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/33.png

Fill out the following fields and click **Continue**:

- **StoreFront URL** - *http://xd.ntnx.local*
- **Receiver for Web Path** - */Citrix/StoreWeb*
  .. note:: Clicking Retrieve Stores should populate this value automatically.
- **Default Active Directory Domain** - *NTNX*
- **Secure Ticket Authority URL (typically your DDC)** - *http://xd.ntnx.com*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/34.png

Fill out the following fields and click **Continue**:

- **Choose Authentication Type** - *Domain*
- **IP Address** - DC IP address from your Environment Details Worksheet
- **Port** - *389*
- **Base DN** - *DC=NTNX,DC=local*
- **Service account** - *Administrator@ntnx.local*
- **Password** - *nutanix/4u*
- **Server Logon Name Attribute** - *sAMAccountName*

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/35.png

Click **Done**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/36.png

In the **NetScaler web console**, select **NetScaler Gateway > Virtual Servers**. Select your vServer and click **Edit**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/37.png

Change **Portal Theme** to **RfWebUI** and click **OK > Done**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/38.png

Select **XenApp and XenDesktop** from the menu and click **Download File**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/39.png

Select **Export all the virtual servers** and click **OK**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/40.png

In **File Explorer**, copy **GatewayConfig.zip** to **\\\\<XD-IP-ADDRESS>\\c$\\**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/41.png

Importing NetScaler Configuration
+++++++++++++++++++++++++++++++++

In **Citrix Studio > Citrix StoreFront**, right-click **Stores > Manage NetScaler Gateways**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/42.png

Click **Imported from file**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/43.png

Click **Browse** and select **C:\\GatewayConfig.zip**. Click **Import**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/44.png

Click **Next**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/45.png

Click **Next**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/46.png

Click **Import**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/47.png

Click **Finish > Close > Close**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/48.png

Note our Store now allows access from both Internal and External networks.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/49.png

Configuring MyDesktop DNS Record
++++++++++++++++++++++++++++++++

In order for our SSL certificate for the mydesktop.ntnx.local vServer to be recognized, we'll have to access StoreFront via the FQDN. You have the option of following the directions below to create a DNS record on your domain controller and ensure the computer you're accessing StoreFront from is using your DC's DNS or you can modify your hosts file to add a static entry as shown below. The second method avoids having to reconfigure your DNS settings on the computer you're using to access StoreFront.

.. note:: In Windows the hosts file is located in C:\Windows\System32\drivers\etc, in macOS the hosts file is located in /etc/

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/50.png

In the **DC** VM console, open **Control Panel > Administrative Tools > DNS**.

In **DNS Manager**, open **DC > Forward Lookup Zones**. Right-click **NTNX.local > New Host (A or AAAA)…**

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/51.png

Fill out the following fields and click **Add Host**:

- **Name** - *mydesktop*
- **IP address** - NetScaler Gateway VIP address from your Environment Details Worksheet

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/52.png

Ensure the client used to run Citrix Receiver is using the **DC** VM for its primary DNS server.

Installing Root Certificate on your Client
++++++++++++++++++++++++++++++++++++++++++

In the **NetScaler web console**, select **Configuration > Traffic Management > SSL** and click **Manage Certificates / Keys / CSRs**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/53.png

Select **root.cer** and click **Download**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/54.png

On the client used to run Citrix Receiver, open the downloaded **root.cer** file and click **Install Certificate**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/55.png

Select **Local Machine** and click **Next**.

.. note:: This may require elevated credentials from a Nutanix resource if performing this workshop within the Nutanix Hosted POC environment.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/56.png

Select **Place all certificates in the following store** and click **Browse**. Select **Trusted Root Certification Authorities** and click **OK**. Click **Next**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/57.png

Click **Finish > OK > OK**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/58.png

Connecting to the Desktop
+++++++++++++++++++++++++

Open *https://mydesktop.ntnx.local* in your browser. Log in as **USER2**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/59.png

Launch a desktop or application and confirm it logs in successfully.

Enabling HTML5 Access
+++++++++++++++++++++

In **Citrix Studio > Citrix StoreFront > Stores**, right-click your **Store Service** and select **Manage Receiver for Web Sites > Configure**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/60.png

Explore this wizard and note this is where basic changes can be made to StoreFront look and feel, timeouts, featured applications, etc.

Select **Deploy Citrix Receiver** and select **Use Receiver for HTML5 if local Receiver is unavailable** from the **Deployment option** drop down menu. Click **OK > Close**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/61.png

Log into StoreFront (via *https://mydesktop.ntnx.local*) as **USER2**. Select **Change Citrix Receiver** from the **USER2** drop down menu.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/62.png

Click **Use light version**.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/63.png

Launch a **Pooled Windows 10 Desktop** and verify that it opens in a new tab in your browser.

.. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab12/64.png

Takeaways
+++++++++

- Support for Citrix products on AHV extends beyond core XenDesktop services. In addition to NetScaler, Citrix ShareFile, Director and Provisioning Services (PVS) are also supported on AHV.
