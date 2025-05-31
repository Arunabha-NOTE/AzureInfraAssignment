# Create and Test Azure Application Gateway

This guide shows you how to set up an Azure Application Gateway to route HTTP traffic to your backend web servers using the Azure portal.

## Prerequisites

* **Azure Account:** You'll need an active Azure subscription.
* **Virtual Network (VNet):** An existing Azure Virtual Network.
* **Dedicated Subnet for Application Gateway:** Create a **separate, empty** subnet within your VNet (e.g., `AppGatewaySubnet` with a `/27` or larger address range like `10.0.2.0/24`). **This subnet must not contain any other resources.**
* **Backend Servers:** You should have at least two VMs within your VNet (in a *different* subnet than the Application Gateway's subnet). These VMs need a basic web server (like IIS on Windows or Nginx/Apache on Linux) running on port 80. Ensure their Network Security Groups (NSGs) allow inbound traffic on port 80 from the Application Gateway's subnet.

---

## Step 1: Create the Application Gateway

1.  In the Azure portal search bar, type "Application Gateways" and select it.
2.  Click **`+ Create`**.

### Basics Tab

* **Subscription:** Select your Azure subscription.
* **Resource Group:** Create a new one or select an existing one (e.g., `my-app-gateway-rg`).
* **Application gateway name:** Enter a unique name (e.g., `my-app-gateway`).
* **Region:** Choose your desired Azure region (e.g., `Central India`).
* **Tier:** Select **`Standard V2`** (recommended for production as it offers higher scale and advanced features).
* **Enable autoscaling:** Set to **`Yes`**.
    * **Minimum scale units:** `0`
    * **Maximum scale units:** `10` (or your desired maximum).
* **Availability zone:** Select `None` for simplicity, or choose zones if applicable.
* **HTTP2:** Keep as `Disabled` for this guide.
* **Virtual network:** Select your existing VNet.
* **Subnet:** Select your **dedicated Application Gateway subnet** (e.g., `AppGatewaySubnet`).

* Click **`Next: Frontends >`**.

### Frontends Tab

* **Frontend IP address type:** Choose **`Public`**.
* **Public IP address:** Select `Add new`, then enter a `Name` (e.g., `my-app-gateway-pip`).

* Click **`Next: Backend pools >`**.

### Backend Pools Tab

* Click **`+ Add a backend pool`**.
* **Name:** `my-backend-pool`.
* **Add backend pool without targets:** Select `No`.
* **Target type:** Choose `Virtual machine`.
* **Target:** Add your backend VMs (e.g., `myWinVM`, `myLinuxVM`) from the dropdown.
* Click **`Add`**.

* Click **`Next: Routing rules >`**.

### Routing Rules Tab

* Click **`+ Add a routing rule`**.
* **Rule name:** `rule1`.

    * **Listener Tab:**
        * **Listener name:** `http-listener`.
        * **Frontend IP:** Select `Public`.
        * **Protocol:** Select `HTTP`.
        * **Port:** Enter `80`.
    * **Backend targets Tab:**
        * **Target type:** Select `Backend pool`.
        * **Backend target:** Select `my-backend-pool`.
        * **HTTP setting:** Select `Add new`.
            * **HTTP setting name:** `http-setting`.
            * **Backend port:** `80`.
            * **Cookie-based affinity:** Set to `Enabled` or `Disabled` based on your preference for session stickiness.
            * **Health probe:** Select `Add new`.
                * **Name:** `my-health-probe`.
                * **Protocol:** `HTTP`.
                * **Port:** `80`.
                * **Path:** `/` (or your specific health check endpoint).
                * **Interval (seconds):** `5`. **Unhealthy threshold:** `2`. **Timeout (seconds):** `5`.
                * Click **`Add`**.
            * Click **`Add`** (for the HTTP setting).
        * Click **`Add`** (for the routing rule).

* Click **`Next: Tags >`**.

### Tags Tab (Optional)

* Add any tags you need for organization.

* Click **`Next: Review + create >`**.

### Review + create Tab

* Review all your configuration settings carefully.
* Click **`Create`**.

The deployment process will now start and usually takes several minutes.

---

## Step 2: Test the Application Gateway

1.  Once the deployment shows as complete, navigate to your **Application Gateway resource** in the Azure portal.
2.  On the **`Overview`** blade, find the **`Frontend public IP address`** or the **`DNS name`**.
3.  Copy the displayed IP address or DNS name.
4.  Open a web browser and paste the copied IP address or DNS name into the URL bar (e.g., `http://<your-app-gateway-ip>` or `http://<your-app-gateway-dns-name>`).
5.  **Expected Result:** You should see the web page served by one of your backend VMs, demonstrating that the Application Gateway is successfully routing traffic.

**Troubleshooting Tips:**
* If the page doesn't load, check the web server status on your backend VMs.
* Ensure the Network Security Groups (NSGs) on your backend VMs allow inbound traffic on port 80 from the Application Gateway's subnet.
* In the Azure portal, check the Application Gateway's **`Backend health`** blade for details on health probe status.