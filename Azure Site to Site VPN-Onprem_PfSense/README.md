# Connect My Homelab to Microsoft Azure with pfSense

This guide details how I connected my homelab to Microsoft Azure by building a site-to-site VPN using pfSense. Initially, I considered using my COX internet router for the task, but since it does not support site-to-site VPN, I turned to pfSense as an alternative. With this setup, I can securely access Azure resources through a private connection. You can find the network diagram on the onprem Home Lab env on this repo begining. 

## Prerequisites

Before starting, ensure you have:

1. A functioning pfSense firewall configured on your local network.
2. An Azure account with administrative access.
3. A configured Virtual Network (VNet) in Azure with an address space that does not overlap with your local network.
   - On-premises network: `192.168.0.0/24`
   - Azure VNet: `10.1.0.0/22`
4. Public IP address of your pfSense firewall.
5. Basic familiarity with pfSense and Azure portal.

---

## Solution Overview

This solution connects your on-premises network to Azure using a secure VPN tunnel. It is suitable for homelab setups but lacks advanced features like packet inspection, which are recommended for business environments. For businesses, consider NextGen Firewall solutions like FortiGate for enhanced protection (e.g., Application Control, Intrusion Prevention, and Advanced Threat Protection).

---

## Steps to Set Up the Site-to-Site VPN

### 1. Configure Azure

1. **Create a Virtual Network Gateway**:
   - Navigate to the Azure portal and search for "Virtual Network Gateway."
   - Click **Create**, and provide the following:
     - Name: `MyVPNGateway`
     - Gateway type: VPN
     - VPN type: Route-based
     - SKU: Basic (for homelab)
     - Virtual Network: Select your existing VNet.
     - Public IP: Create a new public IP for the gateway.
   - Click **Create** and wait for the deployment to complete.

2. **Create a Local Network Gateway**:
   - Go to "Local Network Gateways" in the Azure portal.
   - Click **Create** and provide:
     - Name: `OnPremGateway`
     - IP Address: Public IP of your pfSense firewall.
     - Address Space: `192.168.0.0/24` (your on-prem network).
   - Click **Create**.

3. **Set Up a Connection**:
   - Go to your Virtual Network Gateway and click **Connections**.
   - Click **Add** and configure:
     - Name: `AzureToOnPrem`
     - Connection type: Site-to-Site (IPsec)
     - Virtual Network Gateway: Select your Azure gateway.
     - Local Network Gateway: Select your on-prem gateway.
     - Shared Key: Choose a secure pre-shared key (PSK).
   - Click **OK** to create the connection.

### 2. Configure pfSense

1. **Set Up VPN in pfSense**:
   - Navigate to `VPN > IPsec` in pfSense.
   - Add a new Phase 1 configuration:
     - Key Exchange version: IKEv2
     - Remote Gateway: Public IP of Azure Virtual Network Gateway.
     - Authentication Method: Mutual PSK
     - Pre-Shared Key: Use the PSK configured in Azure.
     - Phase 1 Proposal: AES256, SHA256, DH Group 2 (or Azure-supported values).
   - Save and apply the settings.

2. **Add Phase 2 Configuration**:
   - Go to the Phase 2 tab under IPsec.
   - Configure:
     - Mode: Tunnel IPv4
     - Local Network: `192.168.0.0/24`
     - Remote Network: `192.168.5.0/24`
     - Protocol: ESP
     - Encryption: AES256, SHA256.
   - Save and apply the settings.

3. **Create Firewall Rules**:
   - Navigate to `Firewall > Rules > IPsec` in pfSense.
   - Add a rule to allow traffic between the local network and Azure VNet.

### 3. Test Connectivity

1. From a device in your on-premises network, try pinging a resource in the Azure VNet.
2. Verify that the VPN tunnel is established in both pfSense and Azure (under "Connections").

---

## Conclusion

This guide demonstrates how to establish a site-to-site VPN connection between a homelab using pfSense and Microsoft Azure. While suitable for personal use, consider more robust solutions for business environments.

Feel free to adapt this setup to your needs and explore further options for securing your hybrid environment.

---

## Resources

- [pfSense Documentation](https://docs.netgate.com/pfsense/en/latest/)
- [Azure VPN Gateway Overview](https://learn.microsoft.com/en-us/azure/vpn-gateway/)
- [Configuring a Site-to-Site VPN with pfSense](https://www.netgate.com/resources/)
