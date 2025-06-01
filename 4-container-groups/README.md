# Create Container Instance and deploy a simple docker application on it. Create Container Groups and test functionality

This guide provides the direct steps to deploy a container group in Azure using the Azure portal's visual interface.

---

## Step-by-Step Guide

### 1. Start Creating a Container Instance

* In the **Azure portal search bar** at the top, type "Container Instances" and select `Container Instances` from the search results.
* Click the **`+ Create`** button.

---

### 2. Basics Tab: Configure Project and Container Details

* **Project details:**
    * **Subscription:** Select your Azure subscription.
    * **Resource Group:** Choose an **existing resource group** (e.g., `my-container-rg`).

* **Container details:**
    * **Container name:** Provide a unique name for your container instance (e.g., `my-nginx-web-app-portal`).
    * **Region:** Select the desired Azure region (e.g., `Central India`).
    * **Image source:** Select `Azure Container Registry`.
    * **Registry:** From the dropdown, choose your specific Azure Container Registry (e.g., `myuniqueacrname`).
    * **Image:** Select the Docker image you want to deploy from your ACR (e.g., `nginx:latest`).
    * **Image pull secret:**
        * Check the `Admin user` box if you're using ACR's admin credentials.
    * **Operating system:** This will typically be auto-detected (e.g., `Linux`).
    * **Size:** Adjust **CPU** and **Memory** if needed (defaults are often sufficient).

* Click the **`Next: Networking >`** button to proceed.

---

### 3. Networking Tab: Configure Public Access

* **Network:**
    * **Connectivity type:** Select `Public`.
    * **DNS name label:** Provide a globally unique name (e.g., `my-nginx-aci-portal-app`).
    * **Ports:**
        * **Port:** Enter `80`.
        * **Protocol:** Select `TCP`.

* Click the **`Next: Advanced >`** button.

---

### 4. Advanced Tab (Optional) and Tags Tab (Optional)

* **Advanced:** Configure additional settings if necessary (e.g., restart policy, environment variables).
* **Tags:** Add tags for organization.

* Click the **`Review + create`** button.

---

### 5. Review + create Tab: Final Review and Deployment

* Review all configured settings.
* Click the **`Create`** button.

The deployment process will begin and take a few minutes.

---

### 6. Access Your Running Container Group

* Once deployment is complete, click **`Go to resource`**.
* On the **`Overview`** page of your new container instance, locate the **`FQDN`** (Fully Qualified Domain Name).
* Click on the **FQDN link**, or copy and paste it into your web browser.

You should now see your containerized application (e.g., the default Nginx welcome page).