# Create Azure Load Balancers (External & Internal) via Azure Portal

This guide provides direct, step-by-step instructions for deploying both Public and Internal Azure Load Balancers.

---

## Part 1: Create an External (Public) Load Balancer

### 1. Start Creation

* Go to the Azure portal, search for "Load Balancers," and click `+ Create`.

### 2. Basics Tab

* **Project details:** Select your `Subscription` and `Resource Group` (e.g., `my-load-balancer-rg`).
* **Instance details:**
    * `Name`: `my-external-lb`
    * `Region`: (e.g., `Central India`)
    * `Sku`: `Standard`
    * `Type`: `Public`

* Click `Next: Frontend IP configuration >`.

### 3. Frontend IP Configuration Tab

* Click `+ Add a frontend IP configuration`.
* `Name`: `LoadBalancerFrontEnd`
* `Public IP address`: `Create new`, `Name`: `my-external-lb-pip`, `Sku`: `Standard`.
* Click `Add`.

* Click `Next: Backend pools >`.

### 4. Backend Pools Tab

* Click `+ Add a backend pool`.
* `Name`: `my-web-backend-pool`
* `Virtual network`: Select your VNet (e.g., `myWinVM-vnet`).
* `Add your virtual machines`: Select your VMs (e.g., `myWinVM`).
* Click `Add`, then `Add` again.

* Click `Next: Inbound rules >`.

### 5. Inbound Rules Tab (Health Probe & Load Balancing Rule)

#### 5.1. Health Probe

* Click `+ Add a health probe`.
* `Name`: `my-http-probe`
* `Protocol`: `TCP`, `Port`: `80`.
* Click `Add`.

#### 5.2. Load Balancing Rule

* Click `+ Add a load balancing rule`.
* `Name`: `my-http-rule`
* `Frontend IP address`: `LoadBalancerFrontEnd`
* `Backend pool`: `my-web-backend-pool`
* `Protocol`: `TCP`, `Port`: `80`, `Backend port`: `80`.
* `Health probe`: `my-http-probe`.
* Click `Add`.

* Click `Review + create`.

### 6. Review & Deploy

* Review settings, then click `Create`.

---

## Part 2: Create an Internal (Private) Load Balancer

### 1. Start Creation

* Go to the Azure portal, search for "Load Balancers," and click `+ Create`.

### 2. Basics Tab

* **Project details:** Select your `Subscription` and `Resource Group`.
* **Instance details:**
    * `Name`: `my-internal-lb`
    * `Region`: (e.g., `Central India`)
    * `Sku`: `Standard`
    * `Type`: `Internal`

* Click `Next: Frontend IP configuration >`.

### 3. Frontend IP Configuration Tab

* Click `+ Add a frontend IP configuration`.
* `Name`: `InternalFrontEnd`
* `Virtual network`: Select your VNet.
* `Subnet`: Select the target subnet.
* `Assignment`: `Static`, `Private IP address`: (e.g., `10.0.0.100`).
* Click `Add`.

* Click `Next: Backend pools >`.

### 4. Backend Pools Tab

* Click `+ Add a backend pool`.
* `Name`: `my-internal-backend-pool`
* `Virtual network`: Select your VNet.
* `Add your virtual machines`: Select your VMs.
* Click `Add`, then `Add` again.

* Click `Next: Inbound rules >`.

### 5. Inbound Rules Tab (Health Probe & Load Balancing Rule)

#### 5.1. Health Probe

* Click `+ Add a health probe`.
* `Name`: `my-internal-http-probe`
* `Protocol`: `TCP`, `Port`: `80`.
* Click `Add`.

#### 5.2. Load Balancing Rule

* Click `+ Add a load balancing rule`.
* `Name`: `my-internal-http-rule`
* `Frontend IP address`: `InternalFrontEnd`
* `Backend pool`: `my-internal-backend-pool`
* `Protocol`: `TCP`, `Port`: `80`, `Backend port`: `80`.
* `Health probe`: `my-internal-http-probe`.
* Click `Add`.

* Click `Review + create`.

### 6. Review & Deploy

* Review settings, then click `Create`.