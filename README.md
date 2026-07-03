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
1. **Isolate the Target Resource:** Access the SharePoint Online (SPO) Admin Center to configure a dedicated, isolated communication site collection named `Acquisition-Vault-SPO` and upload a sample sensitive document to the default library.
2. **Provision the Identity Layer:** Access the Microsoft Entra ID (MEID) admin center to provision a pristine test user identity object named `Human-Auditor-User`. Ensure the account maintains a zero-privilege baseline with no group assignments or unneeded licenses.
3. **Configure Scoped Directory Roles:** Assign the `Human-Auditor-User` account to a highly targeted, built-in directory role (such as *Global Reader*) within the directory roles control plane to establish the administrative authorization boundary.

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
