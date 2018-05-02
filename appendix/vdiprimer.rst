Desktop Virtualization FAQ
--------------------------

What is Desktop Virtualization?
+++++++++++++++++++++++++++++++

Desktop Virtualization, or Virtual Desktop Infrastructure (VDI), are terms used to describe a solution where a desktop Operating System (Windows 7, Windows 10, RHEL, etc.) runs as a Virtual Machine on top of a hypervisor in a datacenter. Users connect to the desktop from a client device through a brokering service. The desktop is presented via a remote display protocol, sending screen updates from the VM to the client and peripheral input from the client to the VM. VDI is also often used to generically describe other aspects of End User Computing (EUC), such as server based computing (SBC), user persona management, application virtualization, and operations monitoring. Other aspects of EUC that fall outside the umbrella of VDI include physical device management, software patch management, and mobile device management.

While there are many different motivations to implement a VDI solution, a common goal can be defined as: *Delivering a uniform desktop experience to any user in any location from any device.*

The goal of this workshop is to provide a functional understanding of the components of a Citrix XenDesktop solution, benefits, common challenges, solution design, and the role Nutanix plays in this important workload. In the lab exercises you will deploy a fully functional XenDesktop environment running on top of the Nutanix AHV hypervisor.

Why Desktop Virtualization?
+++++++++++++++++++++++++++



Why NOT Desktop Virtualization?
+++++++++++++++++++++++++++++++
.. inability to focus on app/profile experience due to infrastructure challenges
.. enter Nutanix
.. enter AHV
.. resiliency

.. why not DaaS

Designing a VDI Solution
++++++++++++++++++++++++

.. resiliency?

Discovery
.........

Provisioning
............

Sizing
......

Scalbility of components - refer to RA

Further Reading
+++++++++++++++

.. vdi vs xenapp licensing
.. http://www.carlstalhood.com/xaxd/xenappxendesktop-7-15-ltsr/
