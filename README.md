# Identity Architecture Lab: Dual-Path Data Ingestion Pipeline

### Project Description & Deployment Scenario
This project establishes a zero-trust, dual-path access control architecture to protect a sensitive asset repository inside a clean Microsoft Entra ID (MEID) tenant. The business requirement mandates that an ultra-sensitive corporate acquisition document residing in a secure document library must be accessible through exactly two isolated, independent paths with zero ambient directory exposure. 

To achieve this configuration, the architecture orchestrates five distinct identity security engines from the master tracking inventory: Microsoft Entra Built-In & Custom Roles (Tool 2), Microsoft Entra Directory Objects (Tool 4), App Registrations & Core Service Principals (Tool 22), Enterprise Applications Management (Tool 27), and Microsoft Entra Monitoring & Log Analytics (Tool 35). Path 1 isolates a human auditor profile whose entry relies strictly on highly scoped directory role mapping. Path 2 provisions an automated, non-human artificial intelligence (AI) data ingestion daemon that accesses the asset programmatically via scoped web API claims, completely eliminating interactive authentication prompts.

---

### Impacted System Inventory

* **Tool 2:** Microsoft Entra Built-In & Custom Roles
* **Tool 4:** Microsoft Entra Directory Objects (Users and Groups)
* **Tool 22:** App Registrations & Core Service Principals (AR/SP)
* **Tool 27:** Enterprise Applications Management
* **Tool 35:** Microsoft Entra Monitoring & Log Analytics

---

### Project Execution Outline
#### Phase 1: Target Resource & Human Identity Provisioning

1. **Provision the Base Resource Shell:** Navigate to the SharePoint Online (SPO) Admin Center to configure a dedicated, isolated communication site collection named `Acquisition-Vault-SPO`. Advance through the creation wizard to the member assignment interface screen, and click **Finish** (or **Skip**) without assigning any human or non-human identities to ensure the site remains an isolated container accessible only to the primary tenant administrator.
2. **Provision the Human Identity Layer:** Navigate to the Microsoft Entra ID (MEID) admin center. Go to **Identity** > **Users** > **All Users** and select **New user** > **Create new user**. Configure the user directory object profile with the user principal name `Human-Auditor-User` and display name `Human Auditor User`. Leave all group assignments, administrative units, and subscription licenses completely blank to maintain a true zero-privilege baseline, then finalize object creation.
3. **Configure Scoped Directory Roles:** Locate the newly created `Human Auditor User` within the **All Users** control plane index list. Navigate to the user's specific identity profile blade, select **Assigned roles** under the manage menu section, and click **Add assignments**. Search for and select the built-in **Global Reader** directory role, then change the **Assignment type** toggle from *Eligible* to **Active** on the settings tab to permanently bind this administrative directory-plane scope to the user identity.
4. **Establish Data Plane Linkage:** Locate the `Acquisition-Vault-SPO` site entry within the **Active sites** list of the SharePoint Online (SPO) Admin Center. Check the selection circle next to the site name, click the **Membership** button on the top command bar to fly out the modern details management panel, and look under the **Site visitors** permission header section. Click **Add visitors**, search for and select the `Human Auditor User` account from the matching directory pop-up index list, clear any ambient notification choices, and click **Save** to commit the permission record and enforce strict read-only access to the document library plane.
5. **Populate the Target Resource Asset:** Open a strict InPrivate or Incognito browser session to block ambient operating system credential inheritance, and navigate directly to the tenant-scoped SharePoint Online (SPO) site collection URL interface (`https://lab20250106.sharepoint.com/sites/Acquisition-Vault-SPO`). Click the primary **Documents** library navigation link on the left-hand menu, drag and drop a sample binary file into the document grid, and rename the target asset item to `Sensitive-Acquisition-Asset.pdf` to finalize the resource deployment state.

#### Phase 2: Non-Human Workload Vector Architecture

1. **Register the Application Identity:** Navigate to the Microsoft Entra ID (MEID) admin center and expand the **Applications** menu to select **App Registrations**. Click **New registration** and configure the application object with the display name `AI-Ingestion-Daemon`. Ensure the supported account type toggle is restricted to **Accounts in this organizational directory only (Single tenant)**, leave the optional Redirect URI configuration completely blank to maintain a secure non-interactive daemon posture, and click **Register**.
2. **Expose the Application Footprint:** Copy the generated **Application (client) ID** and the **Directory (tenant) ID** from the overview blade of your new registration and document them for your script variables. Navigate out of the registration area and go directly to **Identity** > **Applications** > **Enterprise Applications**. Verify that a matching enterprise **Service Principal (SP)** footprint object was automatically instantiated in this database backend under the exact name `AI-Ingestion-Daemon`.
3. **Generate Client Credentials Secure Secret:** Return to the **App Registrations** blade and select your `AI-Ingestion-Daemon` registration. Click on **Certificates & secrets** in the left-hand navigation menu, select the **Client secrets** tab, and click **New client secret**. Input a clear description (such as `AI-Ingestion-Token-Key`), set the expiration duration to your preference, and click **Add**. *CRITICAL STEP: Immediately copy the clear-text string listed under the **Value** column and store it securely; this secret represents the non-human programmatic credential and will vanish from visual display permanently once the page refreshes.*
4. **Wire Programmatic Microsoft Graph API Permissions:** Navigate to the **API permissions** blade within the `AI-Ingestion-Daemon` registration workspace and click **Add a permission**. Select the **Microsoft Graph** tile from the request pane, and explicitly select the **Application permissions** option to bypass human interactive login flows. Use the permissions search field to locate **Sites**, expand the object tree, select the checkbox for **Sites.Selected**, and click **Add permissions** at the bottom of the pane.
5. **Execute Tenant-Wide Administrative Consent:** Inspect the API permissions grid list and note the warning status stating that consent has not been granted for the newly added scope. Click the prominent **Grant admin consent for [Your Tenant Name]** button located directly above the permissions table layout. Click **Yes** on the confirmation prompt window to clear the warning gate, transforming the status indicator into a verified green checkmark, which fully authorizes the daemon's authorization scope across the directory plane.

#### Phase 3: Scoped Graph Authorization & Verification

1. **Extract Target Site Collection Metadata Identification:** Open a local PowerShell console or access the web-based Microsoft Graph Explorer interface. Authenticate with tenant global administrator credentials and execute an HTTPS GET request targeting the Microsoft Graph API (MG-API) site utility endpoint to resolve the immutable, tenant-wide site identifier string.
   ```powershell
   # Launch an interactive Graph module session with necessary administrative discovery scopes
   Connect-MgGraph -Scopes "Sites.Read.All"

   # Query the API endpoint to extract the unified, three-part immutable Site ID string
   $TargetSite = Get-MgSite -SiteId "lab20250106.sharepoint.com:/sites/Acquisition-Vault-SPO"
   $TargetSite.Id
   # Record the resulting long output string (format: tenant.sharepoint.com,guid,guid) for use as your $SiteId variable

### Addendum: Field Engineering Notes
* *(Use this section to log live configuration adjustments, out-of-order operations, or unexpected permission gates encountered during tenant execution).*

---

### Lab Results & Verification Outcomes
* *(Use this section to document the final successful token verification strings, confirmed access logs, and final repository outcomes).*
