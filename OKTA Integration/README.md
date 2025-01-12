# OKTA Integration with Azure AD and Office 365

This repository documents the process of integrating OKTA with Azure Active Directory(Entra ID) (Azure AD), Active Directory, using Azure AD as the Identity and Access Management (IAM) solution. Additionally, it includes steps to deploy Single Sign-On (SSO) applications on the OKTA dashboard.

## Overview

This project integrates OKTA, Azure AD, and Office 365 to establish a robust IAM and SSO framework. The integration enables seamless authentication, improved security, and automated license provisioning across applications and services.

### Key Components:
- **OKTA**: Used as the SSO platform for cloud application access.
- **Azure AD**: Serves as the primary IAM system.
- **Office 365**: Provides user productivity tools and will be integrated into OKTA for SSO.
- **Hybrid Cloud Identity**: Local AD synced to Azure AD via Entra ID Connect, enabling hybrid identity management.

---

## Project Prerequisites

### Tools and Software
- Azure AD (Microsoft Entra ID)
- OKTA Developer Account
- Office 365 subscription
- VMware Workstation (optional for lab setup)
- pfSense for network management (if applicable to your environment)

### Environment Setup
1. **Local AD Configuration**
   - Deploy Windows Server 2019 as a Domain Controller.
   - Create and configure a domain (e.g., `vegas-IT.local`).
   - Add users and groups.

2. **Azure AD Integration**
   - Sync local AD with Azure AD using Azure AD Connect.
   - Verify synchronization of users and groups.

3. **Office 365 Setup**
   - Use a custom domain (e.g., `vegas-it.com`) registered on AWS Route 53.
   - Integrate the custom domain with Office 365.

---


# User Directories and the Cloud:

Microsoft Active Directory (AD) plays a central role in coordinating identity and access management policies. AD typically serves as the "source of truth" for user identities and provides access control to on-premises resources such as networks, file servers, and web applications. When on-premises applications are integrated with Active Directory, users benefit from a seamless experience: they log in to their domain once and gain access to the appropriate resources. Administrators also maintain clear control over access permissions. This model has been widely adopted because it works effectively within LAN-based architectures, where applications are hosted on hardware inside the firewall. However, as enterprises increasingly shift to cloud-based applications, this approach faces limitations, necessitating a new solution.

![alt text](<Sub_images/Local Network Diagram.png>)

The shift to cloud applications has led to the proliferation of separate user stores, with each application maintaining its own user credentials. While manageable with one or two applications, this becomes unmanageable as more are adopted, leading to password sprawl and loss of administrative control. Deactivating accounts for departing employees becomes complex, with limited auditing or timely deprovisioning, further compounding the challenge.

Okta’s cloud-based identity and access management service solves these problems with a single integration point that provides a highly available solution for all cloud
and web-based application AD and LDAP integrations.

Okta provides a simple directory integration solution for both cloud and on-premises web applications. Its Identity and Access Management service offers user authentication, provisioning, de-provisioning, and analytics. Okta’s directory integration is easy to set up, highly available, and supports thousands of applications through the Okta Application Network (OAN). Key components for AD integration include:

• Okta Active Directory Agent: A lightweight agent that can be installed on anyWindows Server and is used to connect to on-premises Active Directory for user provisioning, deprovisioning, and authentication requests.
• Okta Integrated Windows Authentication (IWA) Web Application: A lightweight web application that is installed on an Internet Information Services (IIS) and is used to authenticate domain users via Integrated Windows Authentication.
• Okta Active Directory Password Sync Agent: A lightweight agent installed on your domain controllers that will automatically synchronize AD password changes, send to Okta, and keep your user’s AD passwords in sync with the apps they use.

![alt text](Sub_images/okta-ad.png)

1. Simple and Secure Setup and Configuration  
2. Real-time Provisioning  
3. Intelligent User Synchronization  
4. Just-in-time User Provisioning  
5. Robust Delegated Authentication  
6. Integrated Desktop Single Sign-On (SSO) (AD Only)  
7. Self-Service Password Reset Support (AD Only)  
8. Security Group-Driven Provisioning  
9. Automated One-Click Deprovisioning  
10. Single Sign-On for Directory Authenticated Apps  

## Integration Steps

You can create a developer account using a company email. I purchased a domain and hosted it on NameSilo, which offers email domain services for just $3. I used one of these email addresses to start a trial account. Note that it’s unlikely you’ll be able to complete this process using a personal email, such as Gmail.

During installation, you simply provide your Okta URL and AD Administrator credentials. The Okta AD Agent then automatically creates a low-privileged, read-only integration account and securely establishes a connection to your Okta instance—no additional network or firewall configuration is required.

The Okta AD Agent connects to Okta’s cloud service using an outbound SSL connection over port 443. This connection is refreshed every 30 seconds to ensure compatibility with existing firewalls or other security devices.

As a general rule, if a user can log into the host machine with their AD credentials and access the internet via a browser, the Okta AD Agent will operate successfully without requiring any firewall changes.

I have installed it one of my member server / No need to install on the Domain Controller. 

![alt text](Sub_images/OKTA_connected.png)


![alt text](Sub_images/Integrated_attributes_selection.png)

Directory Integrated 

![alt text](Sub_images/oktaintegratedwithAD.png)

To enable user provisioning and deploy Office 365 on Okta, follow these steps:

1. Enable User Provisioning:
Create Security Groups in On-Premises AD:
Create a security group in your local Active Directory (e.g., XYZ_App_License_Group).
Add users who need access to the specific application (e.g., Office 365) to this group.
Sync Groups to Okta:
Use the Okta AD Agent to sync the created security group from Active Directory to Okta.
Confirm the group appears in Okta under Groups after the sync is complete.
Assign Licenses in Okta:
In the Okta Admin Console, go to Applications > Applications.
Find and select the Office 365 application.
Assign appropriate licenses (e.g., Office 365) to the Okta group you created.
2. Deploy Office 365 on Okta Dashboard:
In the Okta Admin Console:
Navigate to Applications > Applications.
Search for Office 365 and add it as an application.
Configure SSO settings using the SAML details provided by your Azure AD for Office 365.
Test the integration by logging in to Okta and accessing Office 365.

