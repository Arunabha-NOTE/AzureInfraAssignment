# Azure Storage Account: Create, Manage, and Utilize

This guide covers creating an Azure Storage Account, managing blobs, understanding authentication methods, applying lifecycle policies, creating file shares, and setting up Azure File Sync.

---

## Part 1: Create an Azure Storage Account & Explore Options

### 1. Start Creation

* In the Azure portal search bar, type "Storage accounts" and select it.
* Click **`+ Create`**.

### 2. Basics Tab: Core Configuration

* **Subscription:** Select your Azure subscription.
* **Resource Group:** Create a new one or select an existing one (e.g., `my-storage-rg`).
* **Storage account name:** Enter a globally unique name (e.g., `mystorageacct12345`).
* **Region:** Select your desired region (e.g., `Central India`).
* **Performance:**
    * **`Standard`**: Good for general-purpose use, including blobs, files, queues, and tables.
    * **`Premium`**: Offers consistently low-latency for specific workloads.
    * *Choose `Standard` for this guide.*
* **Redundancy:**
    * **`LRS (Locally-redundant storage)`**: 3 copies in one data center (lowest cost).
    * **`GRS (Geo-redundant storage)`**: 6 copies total (3 local, 3 in a paired region), for disaster recovery.
    * **`RA-GRS (Read-access geo-redundant storage)`**: GRS with read access to the secondary region.
    * **`ZRS (Zone-redundant storage)`**: 3 copies across 3 availability zones in one region.
    * **`GZRS (Geo-zone-redundant storage)`**: Combines ZRS with geo-replication.
    * *Choose `GRS` for this guide.*

* Click **`Next: Advanced >`**.

### 3. Advanced Tab: Feature Configuration

* **Security:**
    * `Require secure transfer for REST API operations`: **`Enabled`** (recommended for HTTPS).
    * `Enable blob public access`: **`Disabled`** (recommended for security).
    * `Minimum TLS version`: **`TLS 1.2`** (recommended).
* **Data Lake Storage Gen2 (Hierarchical namespace):**
    * `Enable hierarchical namespace`: **`Disabled`** (unless needed for analytics with ADLS Gen2).
* **Blob storage:**
    * `Default to Azure Active Directory authorization in the Azure portal`: **`Enabled`** (recommended).
    * `Access tier`:
        * **`Hot`**: For frequently accessed data (lower access cost, higher storage cost).
        * **`Cool`**: For infrequently accessed data (lower storage cost, higher access cost).
        * **`Archive`**: For rarely accessed data (lowest storage cost, highest access cost, high retrieval latency).
        * *Choose `Hot` as the default.*
* **Network connectivity:**
    * `Connectivity method`: **`Public endpoint (all networks)`** (simplest for testing).

* Explore other tabs (Data protection, Encryption, Tags) as needed.
* Click **`Review + create`**.

### 4. Review & Create

* Review all settings.
* Click **`Create`**.

---

## Part 2: Blob Storage: Upload, Access & Auth

### 1. Upload and Access a Blob

* Navigate to your new Storage Account.
* In the left menu, under `Data storage`, click **`Containers`**.
* Click **`+ Container`**.
    * `Name`: `myblobcontainer`
    * `Public access level`: **`Private (no anonymous access)`** (recommended).
    * Click **`Create`**.
* Click on `myblobcontainer`.
* Click **`Upload`**.
    * Select a file from your local machine.
    * Click **`Upload`**.
* To access: Click on the uploaded blob, then copy its **`URL`** from the properties. Accessing it directly will require authentication due to the `Private` access level.

### 2. Different Authentication Technologies & Testing

#### 2.1. Access Keys (Shared Key Authorization)

* In your Storage Account, in the left menu, under `Security + networking`, click **`Access keys`**.
* Click **`Show keys`**.
* Note `key1` and its **`Connection string`**. This key grants full control.
* **Test (via Azure Storage Explorer):**
    1.  Download and install [Azure Storage Explorer](https://azure.microsoft.com/en-in/products/storage/storage-explorer/).
    2.  Open Storage Explorer. Click the "Connect" icon.
    3.  Select `Use a connection string`.
    4.  Paste the **`Connection string`** from your Storage Account's `Access keys` blade.
    5.  You can now manage your blobs and containers.

#### 2.2. Shared Access Signature (SAS)

SAS provides time-limited, granular access to specific resources without sharing account keys.

* In your Storage Account, in the left menu, under `Security + networking`, click **`Shared access signature`**.
* **Allowed services:** Check `Blob`.
* **Allowed resource types:** Check `Container`, `Object`.
* **Allowed permissions:** For testing, check `Read`, `List`.
* **Start and expiry date/time:** Set an expiry soon (e.g., 1 hour from now).
* **Protocol:** `HTTPS only`.
* Click **`Generate SAS and connection string`**.
* Note the **`Blob SAS URL`**.
* **Test (via browser):** Paste the **`Blob SAS URL`** into a browser. You should be able to view/download the blob within the defined time and permissions.

#### 2.3. Stored Access Policy over Shared Access Signature

Stored Access Policies offer centralized control for SAS, allowing revocation and shared management.

* In your Storage Account, go to `Containers`, then click on `myblobcontainer`.
* In the container's left menu, click **`Access policy`**.
* Click **`+ Add policy`**.
    * `Identifier`: `my-stored-policy`.
    * `Permissions`: Check `Read`, `List`. (Leave start/expiry blank).
    * Click **`OK`**.
* Now, generate a SAS that uses this policy:
    * Go to your Storage Account's `Shared access signature` blade.
    * Under `Allowed resource types`, select `Container`, `Object`.
    * Under `Stored access policy`, select **`my-stored-policy`**.
    * Click **`Generate SAS and connection string`**.
    * Note the **`Blob SAS URL`**.
* **Test (via browser):**
    * Access the blob using this new SAS URL. Access should be limited to `Read` and `List` permissions.
    * **Revocation Test:** Go back to the `Access policy` of `myblobcontainer`, delete `my-stored-policy`, and save. The SAS URL generated from this policy will now be invalid.

### 3. Different Access Tiers and Their Use Cases

* **Hot Tier:** For **frequently accessed data**. Optimal for active data, high-performance applications, and short-term analysis. Lowest access costs, highest storage costs.
* **Cool Tier:** For **infrequently accessed data** (accessed less than once a month, stored for at least 30 days). Suitable for short-term backup, disaster recovery, and older content. Lower storage costs, higher access costs than Hot.
* **Archive Tier:** For **rarely accessed data** (accessed less than once a year, stored for at least 180 days). Ideal for long-term archives, regulatory compliance, and historical data. Lowest storage costs, highest access costs, and retrieval can take hours.

### 4. Apply Lifecycle Management Policy

Automate tiering or deletion of blobs.

* In your Storage Account, in the left menu, under `Data management`, click **`Lifecycle management`**.
* Click **`Add rule`**.
* **Details Tab:**
    * `Rule name`: `move-to-cool-after-30-days`.
    * `Scope`: `Apply rule to all blobs in your storage account` (or specific containers).
    * `Blob type`: `Block blobs`.
    * `Base blobs`: Check `Move to cool storage`.
        * `Days after last modification`: `30`.
* (Optional) Use the `Filters` tab to apply to specific blobs.
* Click **`Add`**.
* **Test:** This policy will automatically transition blobs meeting the criteria (e.g., 30 days since last modification) to the Cool tier. Real-time testing of the time-based trigger isn't feasible in a quick demo, but the policy will be active.

### 5. Test Object Replication in Blob

Object Replication asynchronously copies block blobs between source and destination storage accounts.

* **Prerequisites:** You need two `Standard` or `Premium` storage accounts in different regions, both with `GRS`, `RA-GRS`, `ZRS`, `GZRS`, or `RA-GZRS` redundancy. Create containers in both for source and destination (e.g., `source-container`, `destination-container`).
* **Steps:**
    1.  In your **source Storage Account**, in the left menu, under `Data management`, click **`Object replication`**.
    2.  Click **`Create replication rule`**.
    3.  **Source account details:** Select your source subscription and storage account (`Source-SA`), then select your `source-container`.
    4.  **Destination account details:** Select your destination subscription and storage account (`Destination-SA`), then select your `destination-container`.
    5.  Review and click **`Create`**.
* **Test:**
    * Upload a new blob to the `source-container` in your `Source-SA`.
    * Wait a short period (usually seconds to minutes).
    * Check the `destination-container` in your `Destination-SA`. The uploaded blob should appear there as a replicated copy.

---

## Part 3: Create a File Share & Test Functionality

Azure File Shares offer fully managed file shares accessible via SMB/NFS.

### 1. Create a File Share

* Navigate to your Storage Account.
* In the left menu, under `Data storage`, click **`File shares`**.
* Click **`+ File share`**.
    * `Name`: `mysharedfiles`.
    * `Tier`: **`Transaction optimized`** (default for general use).
    * Click **`Create`**.

### 2. Test Functionality

* Click on your new file share (`mysharedfiles`).
* **Upload:**
    * Click **`Upload`**, select a file from your local machine, and click **`Upload`**.
* **Connect:**
    * Click **`Connect`**.
    * Select your OS (Windows, Linux, or macOS).
    * Copy the provided connection script/command. This script will include your storage account name and one of its access keys.
    * **For Windows (example):** Open PowerShell, paste the command (e.g., `net use Z: \\<storage-account-name>.file.core.windows.net\mysharedfiles /u:AZURE\<storage-account-name> <storage-account-key>`).
    * After executing the command, the file share will be mapped as a network drive (e.g., `Z:`). You can then drag-and-drop files to/from it.

---

## Part 4: Create Azure File Sync

Azure File Sync centralizes your organization's file shares in Azure Storage while maintaining the flexibility, performance, and compatibility of an on-premises file server.

### 1. Create a Storage Sync Service

* In the Azure portal search bar, type "Storage Sync Services" and select it.
* Click **`+ Create`**.
* **`Subscription`**, **`Resource Group`**, **`Name`** (e.g., `my-sync-service`), **`Region`** (same as your storage account).
* Click **`Review + create`**, then **`Create`**.

### 2. Register Your Server (Conceptual)

* Navigate to your new Storage Sync Service.
* In the left menu, click **`Registered servers`**.
* On your **on-premises Windows Server** (or Azure VM that acts as the file server):
    * Download and install the Azure File Sync agent.
    * Follow the agent installation wizard to sign in with your Azure credentials and register the server with your `my-sync-service`.
* Once registered, your server will appear in the `Registered servers` list in the portal.

### 3. Create a Sync Group

* In your Storage Sync Service, in the left menu, click **`Sync groups`**.
* Click **`+ Sync group`**.
* **`Sync group name`**: (e.g., `my-files-sync-group`).
* **`Subscription`**: Your subscription.
* **`Storage account`**: Select the storage account containing your file share.
* **`Azure File Share`**: Select `mysharedfiles` (the one you created in Part 3).
* Click **`Create`**.

### 4. Add a Cloud Endpoint

* Once the Sync Group is created, click on it.
* The chosen Azure File Share (`mysharedfiles`) will automatically be listed as the **`Cloud endpoint`**.

### 5. Add a Server Endpoint

* Under `Server endpoints`, click **`+ Add server endpoint`**.
* **`Registered server`**: Select your registered Windows server.
* **`Path on server`**: Enter the local folder path on your server you want to synchronize with Azure (e.g., `D:\SyncedFolder`).
* Click **`Create`**.

* **Test:** Files placed in `D:\SyncedFolder` on your Windows server will automatically synchronize with `mysharedfiles` in Azure, and vice-versa, creating a two-way synchronization.