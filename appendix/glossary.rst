.. _glossary:

Glossary
--------

Remote Display Protocol
+++++++++++++++++++++++

A protocol whose primary purpose is to display the screen of a computer from a host to a client over a network. The protocol may use TCP, UDP, or a combination of both. Common remoting protocols include VNC, Microsoft RDP, Teradici PCoIP, and Citrix ICA/HDX. Different protocols offer different sets of capabilities for delivering remote displays such as:

* Ability to deliver a high fidelity experience over WAN conditions that may involve limited bandwidth and high latency
* Support for USB devices connected to the client and remoted to the host
* Client location based services like default printer selection
* Native client support for multiple platforms (Windows, macOS, Linux, iOS, Android, etc.)
* Client support for sessions delivered via HTML5

Evaluating the capabilities of the remoting protocol can be an important aspect of choosing a virtual desktop solution.

Citrix ICA
++++++++++

Lorem

Citrix HDX
++++++++++

Ipsum

Client
++++++

The client can refer to either the device or software used to connect to the virtual desktop. It is responsible for providing a local display and inputs (Keyboard, Mouse, etc.) to and from the host using the remote display protocol. Client devices can be broken down into four categories:

* Traditional devices - Desktops and laptops running client software are typically the most flexible end points for accessing a virtual desktop. It is common for personally owned laptops and desktops to be used to remotely access a corporate VDI environment. It is also common for net new virtual desktop deployments to continue to use existing desktops as a means of accessing virtual desktops. This can be beneficial as it offsets the capital cost of investing in VDI and, as minimal local compute is required to run the client software in many scenarios, older desktop models can continue to be used well past their traditional lifespan. The primary downside of using corporate desktops as VDI clients is the need to still patch and maintain those devices, which can detract from the operational benefits of implementing VDI.
* Mobile devices - Devices such as smartphones and tablets are used both as personal devices for remote access as well as corporate owned devices where the mobile form factor is preferred for a particular use case but access to traditional desktop applications is required. Mobile client software can be limited in function compared to traditional devices or thin clients due to limitations of the mobile OS (e.g. limited peripheral support).
* Thin Clients - Thin clients are purpose built devices available from a number of manufacturers including Dell, Lenovo, HPE, IGEL, and others. A thin client runs either an embedded Windows or Linux operating system with client software that may be customized by the OEM. Thin clients come in a variety of different hardware configurations to support use cases ranging from simple kiosks to high end graphics supporting 4+ monitors. Thin clients are typically "locked down" from the perspective that they only allow a user to access their virtual environment, preventing the execution of local workloads. Thin clients benefit from being simpler to patch and maintain, are low cost, have long product lifecycles, and have low power consumption. OEMs and other providers such as ThinKiosk offer software based solutions to re-purpose existing desktop hardware to maintain traditional desktops similarly to thin clients.
* Zero Clients - Zero clients are stateless purpose built devices typically designed to work with a single virtual desktop solution. They may use dedicated proprietary hardware or a very small OS running on ARM or x86 processors and do not support the installation of third party software. While they may not support all features of a display protocol and new display protocol features may lag behind their client software counterparts, zero clients can be the simplest clients to deploy and maintain. They also have low costs, product lifecycles and low power consumption similar to thin clients.

Citrix Receiver
+++++++++++++++

Client software for connecting to a Citrix session. Citrix provides native Receiver clients for Windows, macOS, Linux, iOS, and Android, and HTML5 browsers.

Connection Broker
++++++++++++++++++

The connection broker is a backend infrastructure role that manages all hosted resources (virtual desktops, server desktops, server applications, physical machines), provides the controls necessary to define which users can connect to which resources, and assigning users to the defined resource at user logon. The broker integrates with authentication systems such as Active Directory and also with SSL VPNs to broker WAN connections. Resources are typically allocated by the broker in one of two ways:

* Static/Dedicated - Static assignments refer to a resource being permanently assigned to a specific user. This type of assignment is most commonly used for Persistent virtual desktops where you would want the same user connecting to the same machine at each log on. The assignment can occur manually or automatically during first log on.
* Random/Dynamic/Floating/Shared - Random assignments refer to a user (or group) being assigned to a pool of resources. During each new session the user is randomly assigned to a resource within the pool. This type of assignment is most commonly used for Non-persistent virtual desktops.

Citrix Desktop Delivery Controller (DDC)
++++++++++++++++++++++++++++++++++++++++

Connection broker for XenDesktop deployed as a Windows Server. A single XenDesktop site can contain multiple Delivery Controllers for the purposes of redundancy and scaling out to support increasingly large environments. Delivery Controllers are also responsible for running Machine Creation Services.

Citrix Studio
+++++++++++++

MMC snap-in used for managing a XenDesktop site, including Delivery Controller, StoreFront, and Licensing.

Citrix StoreFront
++++++++++++++++++

XenDesktop infrastructure role deployed as a Windows Server and responsible for consolidating XenDesktop and XenApp resources into a singular application store experience that is consistent across Citrix Receiver sessions. A single XenDesktop site can contain multiple StoreFront servers for the purposes of redundancy and scaling out to support increasingly large environments.

SSL VPN
+++++++

An SSL VPN is a type of VPN that can be used by a standard web browser and unlike traditional VPNs does not require the installation of dedicated client software. Thie lightweight approach is ideal for flexible remote access to a VDI environment when the end user may not have the ability to install software on the client device. SSL VPNs also provide an inherently smaller attack vector for remote connections as you're not extending direct LAN access to a potentially untrusted client device. SSL VPNs can be implemented as physical appliances, purpose built virtual appliances, and as software that can be deployed on a traditional OS.

Citrix NetScaler Gateway
++++++++++++++++++++++++

A role of the Netscaler Application Delivery Controller that provides SSL VPN connectivity from a Citrix Receiver client to a XenDesktop environment. Netscaler can be deployed as either dedicated hardware or a virtual appliance.

Provisioning
++++++++++++

Provisioning refers to the creation of the virtual desktops or servers from a template disk or VM, often referred to as either a "Master" or "Gold" image. The means of provisioning vary widely across VDI solutions.

Citrix Machine Creation Services
++++++++++++++++++++++++++++++++

A method of provisioning VMs for XenDesktop. MCS is a VM creation/orchestration framework installed as part of the Desktop Delivery Controller role and managed through Citrix Studio. COME BACK TO THIS SPOT!

Citrix Provisioning Services
++++++++++++++++++++++++++++

Lorem

Citrix User Profile Management
++++++++++++++++++++++++++++++

Responsible for providing a consistent experience across sessions by storing and applying a user's personal OS and application settings to a virtual desktop or XenApp session. Profiles are stored on network file shares and can be managed via Citrix Studio, Group Policy, or local .ini configuration files. UPM runs as a system service installed as part of the Virtual Delivery Agent within the virtual desktop or XenApp server. Key advantages to implementing UPM versus traditional Microsoft Roaming Profiles include streaming profiles to increase logon speed, administrative controls to limit profile size, and detailed logging.

Citrix Workspace Environment Management
+++++++++++++++++++++++++++++++++++++++

WEM provides user experience management capabilities that complement UPM, replacing user profile and environment settings typically configured by Group Policy or login scripts to the WEM agent, improving management and login speeds. In addition WEM provides resource optimization for virtual desktops and XenApp servers, prioritizing processes within each session to maintain a responsive experience and providing greater per server density. WEM is implemented as an additional agent running beside the VDA in the guest, connected to a WEM Broker service running on multiple load balanced Windows servers and backed by a SQL Server database.

Application Virtualization
++++++++++++++++++++++++++

Technologies such as Microsoft App-V or VMware ThinApp that encapsulate applications from the underlying operating system, allowing a packaged application to run on an OS as though it were natively installed. Packaged applications are stored on network file shares and can be made available on-demand to physical or virtual machines. This on-demand portability makes application virtualization attractive for minimizing the number of master images required to support multiple use cases, installing core applications required by all use cases in the master image and selectively applying virtualized applications to meet the needs of individual groups or users. Application virtualization can also be a useful approach for addressing application conflicts within a single OS, such as needing to run applications side by side that depend on different versions of Java. Application virtualization differs from server based computing solutions such as XenApp in that the application is being executed by the local OS, rather than being hosted by a remote OS and delivered to the local OS via a remote display protocol. Application virtualization has limitations such as the inability to package applications that require services to start at OS boot, applications that include kernel mode device drivers, or applications that use COM+. Application virtualization may also not be an ideal fit for applications, such as Office or browser plug-ins, that require interaction/integration with other packaged apps or natively installed apps.

Application Layering
++++++++++++++++++++

Similar to Application Virtualization, the goal of Application Layering is to disaggregate applications from a base OS image. Application Layering is typically implemented as a file system filter driver, capturing changes to the file system and registry as an app is installed, and does not provide the same encapsulation from the OS as Application Virtualization. In turn, applications can be much simpler to package and application layers can interact with one another and the OS directly, though would not have the same ability to run conflicting applications side by side on the same machine.

Citrix App Layering
+++++++++++++++++++

Formerly Unidesk, Citrix App Layering can dynamically deliver application layers to an image at user logon. Additionally, App Layering can be used to build a golden image from a collection of OS and app layers prior to VM boot. The latter approach allows for the layering of applications that are not typically supported, such as applications that install kernel mode device drivers or system services that start prior to user logon.

Persistent Desktops
+++++++++++++++++++

Similar to a traditional desktops, persistent virtual desktops persist changes to the VM across reboots. Persistent desktops can be VMs provisioned either as full clones of a master image, thin clones that share a base image for reads and independently store writes, or simply from disparate VMs. Persistent desktops offer the greatest flexibility to the user, including the ability to install their own applications and customize OS and application settings without dependence on a remote profile. Once the VM has been provisioned it will require patching/updating through traditional means such as SCCM, WSUS, or other 3rd party patch management tools, limiting the operational benefit of implementing VDI. Persistent desktops have greater storage capacity requirements compared to non-persistent desktops, but this can be mitigated through storage deduplication due to the significant overlap in VM data (application patches, Windows updates, etc.).

.. _glossary-non-persistent-desktops:

Non-Persistent Desktops
+++++++++++++++++++++++

Unlike persistent desktops, non-persistent desktops do not persist changes across VM reboots. Implementations of non-persistent desktops can vary based on the provisioning technology used, but in general, after a user session ends, the VM reverts back to a pristine state. Non-persistent desktops can simplify management operations as changes to the master image can be quickly rolled out (and rolled back) in a consistent fashion to large numbers of VMs. Non-persistent desktops can also eliminate the negative software and performance creep that can occur over time on traditional desktops or persistent virtual desktops. Due to the stateless nature of non-persistent desktops, this approach may not be viable for every use case. For use cases such as kiosks, which require no customization, non-persistent desktops are an ideal fit. For use cases where persisting end user customizations such as application settings are important, profile management solutions would need to be evaluated and employed. Similarly, the need to persist end user data would require additional network based storage. The need to maintain many master images due to varying requirements and application stacks across an organization can also present a challenge for non-persistent desktops at scale. In these cases, application virtualization, application layering, and/or server based application technologies may be applied to consolidate master image sprawl.

Server Based Desktops/Applications
++++++++++++++++++++++++++++++++++

Rather than delivering 1:1 desktop OS VMs to individual users, server based solutions such as Microsoft RDSH and Citrix XenApp offer the ability for multiple users to share the resources of a single machine. These solutions can be used to deliver Windows Server OS desktops (often made to appear as a desktop OS) or individual applications presented seamlessly to the client, making the applications appear as though they were running locally. Servers can be deployed in pools (or silos) responsible for a specific set of applications and can be easily scaled out to support additional users. Server hosted applications delivered seamlessly into virtual desktops is a common approach for minimizing desktop master images while supporting a wide range of application stack requirements. Advantages include lower resource requirements compared to full virtual desktops and less costly Microsoft licensing. Similar to non-persistent desktops, server based solutions also require external profile management and user data storage to address all but the simplest use cases. Other considerations for server based solutions include application supportability on a server OS as well as resource contention among users on the same VM.

Citrix XenApp
+++++++++++++

Lorem

Virtual Delivery Agent
++++++++++++++++++++++

Lorem
