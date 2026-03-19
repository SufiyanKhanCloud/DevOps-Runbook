# Network Debugging & CORS Troubleshooting

When a newly deployed frontend fails to communicate with its backend API, the issue usually lies in network policies or browser security protocols.

## 1. Browser-Level Debugging (The Network Tab)
If the UI loads but data fetches fail or logins are rejected, utilize the browser's Developer Tools.
* Navigate to the **Network Tab** and filter by **Fetch/XHR**.
* Inspect the outgoing requests to verify the destination URL, payload structure, and exact HTTP status code returned by the backend.

## 2. Resolving CORS (Cross-Origin Resource Sharing) Errors
If the Network tab flags a "CORS Error", the backend is actively rejecting the frontend's request for security reasons, usually because the frontend's domain or port does not match the backend's origin.

**The Fix:**
1. Locate the CORS configuration in the backend's core configuration file (e.g., `Program.cs` in .NET Core).
2. Explicitly whitelist the exact IP address, domain, and port of the frontend application using the framework's CORS middleware (`builder.Services.AddCors`).
3. Recompile, publish, and redeploy the backend application.

## 3. Port & Firewall Validation
If the application operates flawlessly when accessed via `localhost` on the server, but times out when accessed via its public or network IP address, the traffic is being dropped at the server's edge.

**The Fix:**
* Audit the **Windows Defender Firewall Inbound Rules**.
* Ensure there is an active, enabled rule explicitly allowing TCP traffic on the specific ports utilized by the application (typically Port `80` for HTTP, `443` for HTTPS, or custom ports like `8080`).
