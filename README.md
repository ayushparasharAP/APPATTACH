START VM ON CONNECT
It lets you reduce costs by enabling users to turn on their session host (VM) only when they need them.
SETUP START VM ON CONNECT!
Start VM on connect can be set up for both personal & pooled session.
A RBAC role is needed for this: "Desktop Virtualization Power On Contributor" to AVD Service Principal.
This role is assigned on the SUBSCRIPTION.
This role can be set up from Azure Portal or PowerShell/CLI.

---
AUTOSCALE
It lets you scale your session host virtual machines in a host pool up or down by turning on/off the session host according to schedule to optimize deployment costs.
NOTE: You can't use autoscale & scale session hosts using Azure Automation & Azure Logic Apps on the same host pool. You must use one or the other. Dynamic autoscaling is only available in Azure and isn't supported in Azure Government.
Scaling plan can be configured for RAMP UP & RAMP DOWN.

---

Scaling plan can be assigned to multiple HOST POOLS in the same resource group.
A host pool can have only one scaling plan assigned to it.
Scaling plan diagnostics can be enabled for troubleshooting purposes.
A scaling plan can have multiple schedules.
To assign a scaling plan, "Desktop Virtualization Power On Off Contributor" role needs to be assigned to the service principal (Azure Virtual Desktop) to the subscription.

---

APP ATTACH
App attach enables you to dynamically attach applications from an application package to a user session in AVD.
Applications are not installed locally on session host, making it easier to manage custom images for session hosts, reducing operational overhead & cost for the organization.
Applications run within containers, which separate user data, the OS and other applications, increasing security and making them easier to troubleshoot.

---

BENEFITS OF APP ATTACH
Applications are delivered using RemoteApp or as part of desktop session.
Permissions are applied per application per user, allowing greater control over which applications users can access.
The same application package can be used across multiple host pools.
Applications can run on any session host in the same Azure region as the app package.
Applications can be upgraded to a new version with a new disk image without the need for a maintenance window.
Users can run multiple versions of the same application concurrently on the same session host.

---

SUPPORTED APPLICATION PACKAGE TYPES AND FILE FORMATS
Package type: MSIX & MSIX bundle → .msix, .msixbundle | Appx & Appx bundle → .appx, .appxbundle | App-V

---

MSIX APP ATTACH
MSIX app attach is Microsoft's application layering technology, designed for AVD which uses the new MSIX package format to separate applications from the core operating system & deliver apps to users dynamically.

---

FEATURES OF MSIX APP ATTACH
Removes need of repackaging | Uses streaming technology to optimize bandwidth by only downloading the different package during an upgrade | Creates separation | The application lives inside a virtual application container which ensures strict isolation between the app & the operating system | Supports silent installation
Capturing process is performed similarly to MSI packaging. MSIX supports converting existing installed MSI packages unattended.
OS remains unmodified. When de-staged, no trace files are left within the system, like the application was never there.
Reduces time it takes for a user to log in | Reduces infrastructure requirements & cost

---

REQUIREMENTS FOR MSIX APP ATTACH

1. AVD deployment with 1 host pool | 2. Windows 10/11 (single or multi-session) | 3. Network share (Azure Files, Azure NetApp Files) | 4. Must have NTFS read permissions & RBAC permissions | 5. MSIX Packaging Tool | 6. msixmgr.exe tool | 7. Internal or external certificate with issuer name = organization name as required for the MSIX

---

MSIX App Attach is a way to deliver MSIX applications to Azure Virtual Desktop (AVD) virtual machines. It allows you to dynamically attach apps from an MSIX package to a user session.

---

It is recommended to create one file share per host pool. It is not recommended to use file share with multiple host pools. It is recommended to use NetApp Files.

---

APPLICATION STATE

1. ACTIVE → MSIX package set to active makes application available to user
2. INACTIVE → MSIX package set to inactive are ignored by AVD or are not added when user signs in

---

APPLICATION REGISTRATION
On Demand Applications are partially registered at sign-in & full registration is postponed until user starts the application (Delayed)
Register at Logon → Each assigned application is fully registered during user logon (logon blocking)

---

DISK IMAGE TYPES
For MSIX App Attach we can use CIMFS (Composite Image File System), VHD or VHDX. Recommended to use CIMFS because it is faster than VHD/VHDX images and consumes less CPU & memory.

---

A CIMFS image is a combination of several files: One .cim file contains metadata | One starting with objectid- | One starting with region- that contains the actual application data

---

IMPORTANT
All MSIX & Appx application packages include a certificate. The certificate needs to be trusted in the environment. Self-signed certificates are supported with appropriate chain of trust.

---

RBAC ROLE ASSIGNMENT
With Azure Files
Entra Joined Scenario → Role: Reader & Data Access to AVD & AVD ARM Provider
ADDS + Entra Joined (Hybrid Scenario) → Role: Storage File Data SMB Share Reader | Default share-level permission

---

TERMINOLOGIES
MSIX is a packaging format | App Attach is a delivery mechanism

---

EXPANDING MSIX
Expanding MSIX means taking the MSIX file and making it an App Attach package.
Steps: 1. Obtain MSIX package (.msix file) | 2. Rename the file to .zip | 3. Unzip the file to folder | 4. Create a VHD with size equal to size of the folder | 5. Mount the VHD | 6. Initialize disk | 7. Create partition | 8. Format the partition | 9. Copy the unzipped content into the VHD | 10. Use MSIXMGR to apply ACLs on the content | 11. Unmount the VHD/CIM

---

Expanding MSIX package is a multi-step process that takes the MSIX file & puts its content into a VHD or CIM file.

---

STAGING
It involves two steps: Mounting the VHD/CIM to the VM | Notify the OS that the MSIX package is available for registration

---

REGISTRATION PROCESS
Registration is per-user basis & explicit registration is required for that user.
Types: 1. Regular registration → Application fully registered | 2. Delayed registration → Application partially registered

---

DEREGISTER
It removes a registered but non-running MSIX package for a user. It happens when the user signs out. During deregistration, application data specific to the user is pushed to the user profile.

---

DE-STAGE
It notifies the OS that an MSIX package application that currently isn't running and is not staged for any user can be unmounted. This removes all references to it in the OS.

---

MSIX PACKAGE STRUCTURE
MSIX | Application | Package | Files

---

APP PAYLOAD
The payload files are the application code files & assets when building the image.

---

APPXBLOCKMAP.XML
The package block map file is an XML file containing a list of the app’s files, including indexes & cryptographic hashes for each block of data stored in the package. The block map file is verified & secured with a digital signature when the package is signed.

---

APPXMANIFEST.XML
The package manifest contains the information needed to deploy, display & update the MSIX. The information includes identity, package dependencies, required capabilities, visual elements, extensible points & basic properties of files.

---

APPSIGNATURE.P7X
This file is generated when the package is signed. All packages are required to be signed before you can use them.

---

CODEINTEGRITY
Ensures the package integrity and validation during deployment.

---

RDP SHORTPATH FOR AVD
RDP Shortpath establishes a UDP-based transport between a local device (Windows app) and session host in AVD.
By default, RDP begins with a TCP-based reverse connect transport, then tries to establish a remote session using UDP.
If the UDP connection succeeds, the TCP connection drops. Otherwise, TCP connection is used as a fallback connection mechanism.
UDP-based transport offers better connection reliability and more consistent latency. TCP-based reverse connect transport provides best compatibility with various networking configurations and has a high success rate for establishing RDP connections.

---

RDP SHORTPATH CAN BE USED IN TWO WAYS

---

1. MANAGED NETWORK
   Where direct connectivity is established between the client device and session host when using a private connection, such as Azure ExpressRoute or Site-to-Site VPN.
   A direct UDP connection is established between the client device & session host.
   Using a managed network is established in one of the following ways:

1) Need to enable the RDP Shortpath listener and allow an inbound port on each session host to accept connections.
2) Using Simple Traversal Underneath NAT (STUN) protocol between a client & session host. Inbound ports on the session host aren't required to be allowed.

---

2. PUBLIC NETWORK
   Where direct connectivity is established between the client & the session host when using a public connection.
   There are two types of public network:
   a) A direct UDP connection using the Simple Traversal Underneath NAT (STUN) protocol between a client & session host.
   b) A relayed UDP connection using the Traversal Using Relay NAT (TURN) protocol between a client & session host.

---

IMPORTANT
To use RDP Shortpath for managed network, you must enable a UDP listener on the session host.
By default, UDP port 3390 is used, but it can be changed to any unused port.

---

WHERE CAN RDP SHORTPATH BE CONFIGURED?
Host Pool → Networking → RDP Shortpath


