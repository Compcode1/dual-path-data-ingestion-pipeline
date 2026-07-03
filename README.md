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
5. **Populate the Target Resource Asset:** Open a new browser tab, navigate directly to the newly provisioned SharePoint Online (SPO) site collection URL interface (`https://[your-tenant-name].sharepoint.com/sites/Acquisition-Vault-SPO`), and click the primary **Documents** library navigation link on the left-hand menu. Drag and drop a sample binary file into the document grid, then rename the target asset item to `Sensitive-Acquisition-Asset.pdf` to finalize the resource deployment state.

#### Phase 2: Non-Human Workload Vector Architecture
1. **Register the Application Identity:** Navigate to the **App Registrations** blade inside Microsoft Entra ID (MEID) and register a new daemon application named `AI-Ingestion-Daemon` configured as a single-tenant application with no interactive redirect Uniform Resource Identifiers (URIs).
2. **Expose the Application Footprint:** Validate that the identity registration successfully instantiates a corresponding local enterprise **Service Principal (SP)** object inside the **Enterprise Applications Management** directory database.
3. **Wire Programmatic Permissions:** Access the API Permissions workspace within the app registration to assign the explicit application-level permission scope `Sites.Selected` under the Microsoft Graph API (MG-API) permission index.
4. **Execute Administrative Consent:** Trigger the tenant-wide administrator consent workflow to authorize the application's programmatic scopes, enabling backend authentication without a user-interactive sign-in loop.

#### Phase 3: Scoped Graph Authorization & Verification
1. **Bind App Identity to Resource Target:** Use a local PowerShell console to run a script targeting the Microsoft Graph API (MG-API) endpoint, explicitly granting read permissions for the `AI-Ingestion-Daemon` service principal object strictly against the `Acquisition-Vault-SPO` site collection.
2. **Validate Path 1 (Human Vector):** Authenticate interactively as `Human-Auditor-User` inside a private browser session and verify that resource entry is successfully granted based on directory role membership.
3. **Validate Path 2 (Non-Human Vector):** Execute an unsupervised OAuth 2.0 Client Credentials token exchange for `AI-Ingestion-Daemon` via PowerShell. Utilize the resulting Access Token (AT) to query the target SharePoint site metadata programmatically to verify the non-interactive data plane connection.

---

### Addendum: Field Engineering Notes
* *(Use this section to log live configuration adjustments, out-of-order operations, or unexpected permission gates encountered during tenant execution).*

---

### Lab Results & Verification Outcomes
* *(Use this section to document the final successful token verification strings, confirmed access logs, and final repository outcomes).*
