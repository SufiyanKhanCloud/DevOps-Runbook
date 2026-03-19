# Resolving SPA Refresh Errors in IIS

## The Problem
When deploying a Single Page Application (SPA) like React, Vue, or Angular to IIS, navigating to a sub-route (e.g., `/dashboard`) and executing a hard browser refresh typically results in a `404 Not Found` or `500 Internal Server` error. 

**Root Cause:** IIS natively attempts to resolve the URL by looking for a physical directory named `dashboard` on the server's hard drive. Because SPAs handle routing entirely on the client-side via `index.html`, this physical folder does not exist, causing the web server to reject the request.

## The Solution: URL Rewrite
To resolve this, the web server must be instructed to intercept all failed directory lookups and redirect them back to the SPA's entry point.

### Implementation Steps
1. **Verify Modules:** Ensure the **IIS URL Rewrite Module 2** is installed on the Windows Server. Applying rewrite rules without this module will cause an immediate application crash.
2. **Web.Config Engineering:** Deploy a custom `web.config` file to the root of the frontend's physical directory (e.g., the `dist` or `build` folder).
3. **Routing Configuration:** Implement a rewrite rule that evaluates incoming traffic. If the requested URL is not a valid physical file (`IsFile negate="true"`) and not a valid directory (`IsDirectory negate="true"`), silently rewrite the request to `/index.html`.

**Sample Configuration:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="SPA Routes" stopProcessing="true">
          <match url=".*" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/index.html" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
