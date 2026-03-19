# Windows Server & IIS Build Pipeline

## The Deployment Flow
To ensure stability and performance in a production environment, raw source code should never be deployed directly to the server. 

### 1. Build & Publish Protocol
* **Backend (.NET Core):** The backend must be compiled using the IDE's Publish tool (e.g., Visual Studio Publish). This generates the optimized binaries (`.dll`, `.exe`). Only these published files are copied to the IIS physical directory.
* **Frontend (React/Vite):** The frontend must be bundled using standard Node.js build scripts (e.g., `npm run build`). This minifies the assets into a static `dist` or `build` directory, which is then deployed to the web server.

### 2. Manual Verification & Health Checks
Before binding the application to IIS, always perform a manual health check:
1. Open the command line interface on the server.
2. Execute the compiled backend binary directly (e.g., `Application.exe`).
3. Verify that the application connects to the database and generates any required startup tables (such as background job queues).
4. **Localhost Testing:** Always test the application via `http://localhost:<port>` on the server itself. If it works locally but fails externally, the issue is at the network/firewall layer, not the application layer.

### 3. State Management (IIS Reset)
When updating application configurations (like connection strings) or recovering from application crashes (e.g., HTTP 500.30 or 500.19 errors), the IIS worker process may cache the broken state.
* **Fix:** Execute `iisreset` in an elevated Command Prompt to flush the server's cache and force a clean application restart.
