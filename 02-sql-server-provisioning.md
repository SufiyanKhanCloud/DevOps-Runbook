# SQL Server Security & Provisioning

## 1. Engine Installation vs. Management Tools
A common pitfall when provisioning a new server is installing only the management tools (like SSMS) without the actual database engine. Ensure **SQL Server 2022 Express** (or the appropriate edition) is actively downloaded, installed, and verified as a running service in `services.msc`.

## 2. Authentication & Account Security
By default, SQL Server enforces strict Windows Authentication, which often blocks external application backends from connecting.

**Security Configuration Steps:**
1. **Enable Mixed Mode:** Access Server Properties > Security, and switch to **SQL Server and Windows Authentication mode**.
2. **Provision the Service Account:** Unlock the default System Administrator (`sa`) account. 
3. **Credential Management:** Assign a highly secure, dedicated password to the `sa` account strictly for use in the application's secure connection string (e.g., `appsettings.production.json`).
4. **Restart:** The SQL Server Engine must be restarted for authentication changes to take effect.

## 3. Firewall Hardening
To allow external applications to query the database, standard network security practices must be implemented.
* Create a **Windows Defender Firewall Inbound Rule** allowing **TCP traffic on Port 1433**.
* *Note:* Internal backend applications hosted on the same server communicating via `localhost` will bypass this rule, but it is required for future-proofing external access.

## 4. Handling Large Data Restorations over RDP
When transferring massive database `.bak` archives over a Remote Desktop Protocol (`\\tsclient`) connection, network drops can cause file corruption.
* **Solution:** Avoid executing or extracting large compressed archives directly over the network tunnel. Transfer the raw archive to the server's local disk, and utilize lightweight, server-grade extraction tools (like 7-Zip) locally to prevent packet loss.
