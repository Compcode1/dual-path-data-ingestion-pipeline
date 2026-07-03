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

Phase 1: Target Resource & Human Identity Provisioning
Step 1: Provision the Base Resource Shell
Navigate to the SharePoint Online (SPO) Admin Center.

Create a new Communication Site and name it Acquisition-Vault-SPO.

When the creation wizard advances to the Add Members / Owners screen, click Finish (or Skip) without entering any names. This leaves the site provisioned as an isolated container accessible only to you (the Global Administrator).

Step 2: Provision the Human Identity Layer
Open a new browser tab and navigate to the Microsoft Entra ID (MEID) Admin Center.

Go to Identity > Users > All Users and click New user > Create new user.

Configure the user identity with the following exact parameters:

User principal name: Human-Auditor-User

Display name: Human Auditor User

Password: Select "Auto-generate password" or create a temporary secure one (copy this down for testing later).

Leave all group assignments, administrative units, and licenses completely blank to ensure a true zero-privilege baseline. Click Review + create, then click Create.

Step 3: Configure Scoped Directory Roles (Directory Plane)
In the All Users list, click on your newly created Human Auditor User to open their account profile.

In the left-hand menu under the Manage section, click on Assigned roles.

Click Add assignments.

Search for and select the Global Reader role.

Click Add to bind this administrative directory-plane scope to the user's identity.

Step 4: Establish the Data Plane Linkage (SharePoint Plane)
Return to your SharePoint site collection: https://[your-tenant-name].sharepoint.com/sites/Acquisition-Vault-SPO

In the top right corner of the site, click on the Settings gear icon and select Site permissions.

Click on Advanced permissions settings at the bottom of the pane to open the classic SharePoint authorization control plane.

Click on the Acquisition-Vault-SPO Visitors group link (this group grants default Read-Only access to the data plane).

Click New > Add Users.

Type Human Auditor User, select the account from the directory pop-up, uncheck "Send an email invitation," and click Share.

Architectural Linkage Note: The user is now securely linked via two distinct controls: they possess directory-wide auditing access via the Global Reader role in Microsoft Entra ID, and explicit data-plane read entry via the SharePoint Site Visitors group mapping.

Step 5: Populate the Target Resource Asset
Click on Documents in the left-hand navigation menu of your Acquisition-Vault-SPO site.

Drag and drop a sample PDF or text file into the library.

Rename this file to Sensitive-Acquisition-Asset.pdf to complete the resource target phase.

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
