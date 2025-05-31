# Azure Network Configuration Guides

## Part A: Single VNet with Two Subnets

This section shows how to create a single Virtual Network with two subnets for different resource types.

---

### 1. Create the Virtual Network

* In the Azure portal search bar, type "Virtual networks" and select it.
* Click **`+ Create`**.
* **Basics Tab:**
    * `Subscription`: Choose your subscription.
    * `Resource Group`: Create new or select existing (e.g., `my-network-rg`).
    * `Name`: `my-single-vnet`
    * `Region`: (e.g., `Central India`)
* Click **`Next: IP Addresses >`**.

### 2. Configure IP Addresses and Subnets

* **IP Addresses Tab:**
    * `IPv4 address space`: (e.g., `10.0.0.0/16`).
    * **Subnets:**
        * The `default` subnet is pre-created. Click on it.
        * Change `Subnet name` to `Subnet-1-VMs`.
        * `Address range`: (e.g., `10.0.0.0/24`).
        * Click **`Save`**.
        * Click **`+ Add subnet`**.
        * `Subnet name`: `Subnet-2-SQL`
        * `Address range`: (e.g., `10.0.1.0/24`).
        * Click **`Add`**.
* Click **`Review + create`**.

### 3. Review & Create

* Review settings, then click **`Create`**.

---

## Part B: Hub and Spoke Architecture with VNet Peering

This section covers setting up a Hub-Spoke network, creating VMs, and verifying connectivity.

---

### 1. Create Four Virtual Networks

Ensure each VNet has **non-overlapping IP address spaces**.

* Repeat the "Create the Virtual Network" steps (from Part A) four times, using these details:
    * **Management VNet (HUB):**
        * `Name`: `Mgmt-VNet`
        * `Address space`: `10.1.0.0/16`
        * `Subnet`: `Mgmt-Subnet` (`10.1.0.0/24`)
    * **Production VNet (Spoke):**
        * `Name`: `Prod-VNet`
        * `Address space`: `10.2.0.0/16`
        * `Subnet`: `Prod-Subnet` (`10.2.0.0/24`)
    * **Testing VNet (Spoke):**
        * `Name`: `Test-VNet`
        * `Address space`: `10.3.0.0/16`
        * `Subnet`: `Test-Subnet` (`10.3.0.0/24`)
    * **Developing VNet (Spoke):**
        * `Name`: `Dev-VNet`
        * `Address space`: `10.4.0.0/16`
        * `Subnet`: `Dev-Subnet` (`10.4.0.0/24`)

* Ensure all VNets are in the **same region** and **same resource group**.

### 2. Configure Hub and Spoke Architecture (VNet Peering)

Each Spoke VNet must be peered with the Hub VNet.

* For **each Spoke VNet** (`Prod-VNet`, `Test-VNet`, `Dev-VNet`):
    1.  Navigate to the Spoke VNet in the Azure portal.
    2.  In the left menu, under `Settings`, click **`Peerings`**.
    3.  Click **`+ Add`**.
    4.  **This VNet (Spoke):**
        * `Peering link name`: (e.g., `Prod-VNet-to-Mgmt-VNet`)
        * `Virtual network`: (should be current Spoke VNet)
    5.  **Remote virtual network (Hub):**
        * `Peering link name`: (e.g., `Mgmt-VNet-to-Prod-VNet`)
        * `Subscription`: Select your subscription.
        * `Virtual network`: Select `Mgmt-VNet`.
        * Ensure **`Allow virtual network access`** is selected for both directions.
    6.  Click **`Add`**.

### 3. Launch VMs in Each VNet

Create one VM in each of the four VNets.

* For **each VNet** (`Mgmt-VNet`, `Prod-VNet`, `Test-VNet`, `Dev-VNet`):
    1.  In the Azure portal, search for "Virtual machines" and click **`+ Create`**.
    2.  **Basics Tab:**
        * `Subscription`: Your subscription.
        * `Resource group`: Your resource group.
        * `Virtual machine name`: (e.g., `Mgmt-VM`, `Prod-VM`, `Test-VM`, `Dev-VM`)
        * `Region`: Same as VNets.
        * `Image`: Select a Linux (e.g., Ubuntu) or Windows Server image.
        * `Administrator account`: Set username and password.
        * `Public inbound ports`: **`None`**.
    3.  **Networking Tab:**
        * `Virtual network`: Select the corresponding VNet (e.g., `Mgmt-VNet` for `Mgmt-VM`).
        * `Subnet`: Select the corresponding subnet (e.g., `Mgmt-Subnet`).
        * `Public IP`: Keep as `(new)` for initial connection.
        * `NIC network security group`: `Basic`.
        * **Post-creation Step:** After the VM is created, go to its Network Security Group (under `Networking` in the VM blade). Add an **inbound port rule** to allow `ICMP` traffic from `Any` source for ping testing.
    4.  **`Review + create`**, then **`Create`**.

### 4. Verify Connectivity (Ping)

* **Connect to `Mgmt-VM`**: Use RDP (for Windows) or SSH (for Linux) via its public IP or Azure Bastion.
* **Identify Private IPs**: In the Azure portal, note the **private IP addresses** of `Prod-VM`, `Test-VM`, and `Dev-VM` from their respective `Overview` pages.
* **Open Terminal/CMD on `Mgmt-VM`**:
    * For Linux: `ping <Prod-VM-Private-IP>`
    * For Windows: `ping <Prod-VM-Private-IP>`
* Repeat the ping commands for `Test-VM` and `Dev-VM`'s private IP addresses.
* **Expected Result**: You should see successful replies from all three VMs, confirming that VNet peering is working and traffic is flowing between the Hub and Spoke VNets.