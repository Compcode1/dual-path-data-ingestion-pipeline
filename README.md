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
   ```

2. **Execute Graph-Based Scoped Authorization Binding:** Open a fresh administrative PowerShell console. Establish a connection to the directory engine using an authorization scope capable of modifying site access policies. Craft and pass a structured payload to bind the `AI-Ingestion-Daemon` application identity directly to the specific `Acquisition-Vault-SPO` resource site container with explicit read-only privileges.
   ```powershell
   # Establish an elevated administrative session to bind the security relationship
   Connect-MgGraph -Scopes "Sites.FullControl.All"

   # Define initialization variables mapping back to your tenant configurations
   $SiteId = "PASTE_YOUR_FULL_THREE_PART_SITE_ID_STRING_HERE"
   $DaemonAppId = "PASTE_YOUR_AI_INGESTION_DAEMON_APPLICATION_CLIENT_ID_HERE"

   # Construct the policy payload targeting the local enterprise Service Principal object
   $PermissionParams = @{
       roles = @("read")
       grantedToIdentitiesV2 = @(
           @{
               application = @{
                   id = $DaemonAppId
                   displayName = "AI-Ingestion-Daemon"
               }
           }
       )
   }

   # Execute the command to inject the targeted permission record into the resource data plane
   New-MgSitePermission -SiteId $SiteId -BodyParameter $PermissionParams
   ```

3. **Validate Path 1 (Human Interactive Vector Access Test):** Launch a strict, unauthenticated InPrivate browser window to enforce clean session isolation. Navigate directly to the data-plane endpoint `https://lab20250106.sharepoint.com/sites/Acquisition-Vault-SPO`. Provide the unique credentials for the `Human-Auditor-User` account when challenged by the directory interface. Verify that the entry state resolves to a successful read-only display of the site dashboard, and attempt to open the file `document.docx`. Confirm that viewing access is permitted but document deletion, folder creation, and structure edits are strictly blocked by the authorization engine.

4. **Validate Path 2 (Non-Human Automated Programmatic Vector Access Test):** Clear your local PowerShell workspace. Execute the following master deployment verification script to simulate the headless background data pipeline engine. This script performs an unattended OAuth 2.0 Client Credentials token exchange directly against the identity endpoint, extracts an Access Token (AT), and uses it to scrape target file library metadata completely free of human-interactive prompts or multi-factor challenges.
   ```powershell
   # -----------------------------------------------------------------------------
   # Heads-Up Background Pipeline Automation Simulation Script
   # -----------------------------------------------------------------------------
   # Populate authorization fields with secrets captured during Phase 2 execution
   $TenantId     = "PASTE_YOUR_DIRECTORY_TENANT_ID_HERE"
   $ClientId     = "PASTE_YOUR_AI_INGESTION_DAEMON_APPLICATION_CLIENT_ID_HERE"
   $ClientSecret = "PASTE_YOUR_CLEAR_TEXT_CLIENT_SECRET_VALUE_HERE"
   $SiteId       = "PASTE_YOUR_FULL_THREE_PART_SITE_ID_STRING_HERE"

   # Step A: Execute background token acquisition via raw HTTPS REST POST exchange
   $TokenUri = "[https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token](https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token)"
   $BodyParams = @{
       client_id     = $ClientId
       client_secret = $ClientSecret
       scope         = "[https://graph.microsoft.com/.default](https://graph.microsoft.com/.default)"
       grant_type    = "client_credentials"
   }

   Write-Host "Initiating OAuth 2.0 Client Credentials Grant Exchange against MEID..." -ForegroundColor Cyan
   $TokenResponse = Invoke-RestMethod -Uri $TokenUri -Method Post -ContentType "application/x-www-form-urlencoded" -Body $BodyParams
   $AccessToken   = $TokenResponse.access_token

   # Step B: Attach the retrieved Access Token (AT) to the header and pull data plane asset metadata
   $AuthHeader = @{
       Authorization = "Bearer $AccessToken"
   }

   $GraphUri = "[https://graph.microsoft.com/v1.0/sites/$SiteId/drive/root/children](https://graph.microsoft.com/v1.0/sites/$SiteId/drive/root/children)"
   Write-Host "Transmitting non-interactive Access Token to target SharePoint asset library..." -ForegroundColor Cyan
   $LibraryContents = Invoke-RestMethod -Uri $GraphUri -Method Get -Headers $AuthHeader

   # Step C: Parse and display data pipeline extraction results to confirm connection
   Write-Host "`n[Ingestion Pipeline Extraction Success Outcome]" -ForegroundColor Green
   foreach ($Item in $LibraryContents.value) {
       [PSCustomObject]@{
           "Asset Name"       = $Item.name
           "Asset ID"         = $Item.id
           "Size (Bytes)"     = $Item.size
           "Last Modified By" = $Item.lastModifiedBy.user.displayName
       } | Format-List
   }
   ```


#### Lab Results & Verification Outcomes

##### Project Executive Summary
This lab successfully established and verified a zero-trust, isolated data extraction architecture for the secure document repository **Acquisition-Vault-SPO**. The primary engineering objective was to construct exactly two independent, strictly confined access pathways into this specific folder structure while ensuring that the rest of the corporate tenant remained entirely hidden from both identities. This pattern mirrors the precise foundational setup used to deploy secure, headless AI ingestion daemons across enterprise cloud environments.

---

##### 1. The Labeled Identities & Configurations (What & How)
To accomplish complete separation of duties, the directory plane was configured with two distinct security principals:

* **The Human Vector (Human-Auditor-User):** An independent user account created natively in Microsoft Entra ID. This identity was assigned a low-level directory monitoring role and explicitly added directly to the SharePoint site collection’s access list as a standard "Site Visitor."
* **The Non-Human Machine Vector (AI-Ingestion-Daemon):** A dedicated Application Registration configured without an interactive Redirect URI to maintain a secure background posture. This identity does not use passwords; instead, it relies on an OAuth 2.0 Client Credentials configuration utilizing a secure **Application Client ID** and a hidden cryptographic **Client Secret** key to authenticate programmatically.
* **The Scoped API Binding Layer:** Using the administrative PowerShell console, the modern Microsoft Graph API granular authorization framework (`Sites.Selected`) was executed. By passing the unique, three-part immutable serial number of the SharePoint site collection, a hard security permission record was injected to grant the background daemon read-only rights to this specific location, leaving it completely blind to all other corporate files.

---

##### 2. Core Engineering Rationales (Why)
* **Elimination of Interactive Prompts:** By utilizing application permissions instead of delegated permissions for the daemon, the system avoids human interactive login prompts. This allows background processing scripts to run automated data ingestion routines seamlessly around the clock.
* **Attack Surface Minimization:** Leaving the application registration’s Redirect URI completely blank ensures that the non-human machine identity can never be hijacked or manipulated into routing interactive user tokens to an external attacker.
* **Granular Principle of Least Privilege:** Using `Sites.Selected` ensures that even if the daemon's client secret were ever compromised, the blast radius is restricted entirely to this single site collection. The daemon has zero lateral visibility into other tenant assets, emails, or user profiles.

---

##### 3. Lab Verification Results
Both pathways were thoroughly tested and validated as fully operational:

* **Path 1 Verification (Success):** Manual authentication as the `Human-Auditor-User` inside a clean, unauthenticated InPrivate browser session confirmed immediate visual read-only access to the document vault. File viewing was fully permitted, while all actions to delete, modify, or add items were strictly blocked.
* **Path 2 Verification (Success):** Execution of a headless background pipeline simulation script utilizing the application credentials successfully bypasses all user interaction. The script hit the token authority endpoint, obtained a cryptographic Access Token, matched the custom SharePoint site permission record via the Microsoft Graph API, and instantly extracted the target file metadata (`Document.docx`) with zero interactive interference.
