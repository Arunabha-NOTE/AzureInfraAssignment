# Setup Domain, Web Server on VM, and Azure DNS
This guide outlines how to host a website on an Azure VM, point a custom domain to it using Azure DNS, and ensure traffic reaches your server.  
**I do not have access to a domain name so couldnt fully follow through all the steps.**

## Part 1: Create a VM and Install Web Server

This assumes you already have an Azure Resource Group and Virtual Network.

### 1. Create a Virtual Machine (VM)

* In the Azure portal search bar, type "Virtual machines" and select it.
* Click **`+ Create`**.
* **Basics Tab:**
    * `Resource Group`: Select your existing resource group.
    * `Virtual machine name`: Give it a name (e.g., `myWebServerVM`).
    * `Region`: Select your desired region (e.g., `Central India`).
    * `Image`: Choose your preferred OS image (e.g., `Windows Server 2022 Datacenter` or `Ubuntu Server 22.04 LTS`).
    * `Administrator account`: Set a username and strong password.
    * `Public inbound ports`: Select **`Allow selected ports`**.
        * For Windows: Ensure `RDP (3389)` and `HTTP (80)` are checked.
        * For Linux: Ensure `SSH (22)` and `HTTP (80)` are checked.
* **Networking Tab:**
    * Ensure a `Public IP` is assigned (the default `(new)` is fine).
* Complete the remaining tabs and click **`Review + create`**, then **`Create`**.
* After deployment, note down the VM's **Public IP address** from its `Overview` blade.

### 2. Install a Web Server

* **Connect to your VM:** Use RDP (for Windows) or SSH (for Linux) via its public IP address.

* **For Windows (IIS):**
    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # You can place your website files in C:\inetpub\wwwroot\
    ```

* **For Linux (Nginx):**
    ```bash
    sudo apt update
    sudo apt install nginx -y
    sudo systemctl start nginx
    sudo systemctl enable nginx
    # You can place your website files in /var/www/html/
    ```

* **Verify Web Server:** Confirm you can access the default web page by opening a browser and navigating to your VM's public IP address.

---

## Part 2: Acquire a Domain & Configure Azure DNS

### 1. Acquire a Custom Domain

* You'll need to **purchase a domain name** from a domain registrar (e.g., GoDaddy, Namecheap, Google Domains). Azure does not directly sell domain names.
* (Example: `yourcustomdomain.com`)

### 2. Create an Azure DNS Zone

* In the Azure portal search bar, type "DNS zones" and select it.
* Click **`+ Create`**.
* **Basics Tab:**
    * `Subscription`: Select your subscription.
    * `Resource Group`: Select your resource group.
    * `Name`: Enter your custom domain name (e.g., `yourcustomdomain.com`).
* Complete the remaining tabs and click **`Review + create`**, then **`Create`**.
* After creation, navigate to your new **DNS Zone**. Note down the **four Name Servers (NS)** listed on the `Overview` blade (e.g., `ns1-01.azure-dns.com`, `ns2-01.azure-dns.net`, etc.).

### 3. Point Domain to Azure DNS Name Servers

* Go to your **domain registrar's website** (where you bought the domain).
* Find the **DNS management** or **Name Server settings** for your domain.
* **Replace your registrar's default name servers** with the **four Azure DNS Name Servers** you noted in the previous step.
* Save the changes.
* **Important:** DNS changes can take anywhere from a few minutes to up to 48 hours to propagate globally.

### 4. Create an A Record in Azure DNS

* In the Azure portal, navigate to your **DNS Zone** (e.g., `yourcustomdomain.com`).
* Click **`+ Record set`**.
* **Add record set:**
    * `Name`: Enter `@` for the root domain (e.g., `yourcustomdomain.com`). If you want `www.yourcustomdomain.com`, use `www` here. You can create separate records for both.
    * `Type`: Select `A`.
    * `TTL`: Keep at `1 hour` or set as desired.
    * `IP address`: Enter the **Public IP address of your web server VM** (the one you noted down in Part 1).
* Click **`OK`**.

---

## Part 3: Test Domain Access

1.  Open a web browser.
2.  Type your custom domain name (e.g., `http://yourcustomdomain.com` or `http://www.yourcustomdomain.com`) into the URL bar.
3.  **Expected Result:** After DNS propagation, you should see the web page served by your Azure VM. If it doesn't work immediately, wait a bit longer for DNS changes to fully update.
