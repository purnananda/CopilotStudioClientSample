# Direct To Engine Sample

## Instructions - Setup

### Prerequisite

To setup for this sample, you will need the following:

1. An Agent Created in Microsoft Copilot Studio, or access to an existing Agent.
1. Ability to Create a Application Identity in Azure for a Public Client/Native App Registration Or access to an existing Public Client/Native App registration with the CopilotStudio.Copilot.Invoke API Permission assigned. 

### Create a Agent in Copilot Studio

1. Create a Agent in [Copilot Studio](https://copilotstudio.microsoft.com)
    1. Publish your newly created Copilot
    1. Goto Settings => Advanced => Metadata and copy the following values, You will need them later:
        1. Schema name
        1. Environment Id

### Create an Application Registration in Entra ID

This step will require permissions to Create application identities in your Azure tenant. For this sample, you will be creating a Native Client Application Identity, which does not have secrets.

1. Open https://portal.azure.com 
1. Navigate to Entra Id
1. Create an new App Registration in Entra ID 
    1. Provide an Name
    1. Choose "Accounts in this organization directory only"
    1. In the "Select a Platform" list, Choose "Public Client/native (mobile & desktop) 
    1. In the Redirect URI url box, type in `http://localhost` (**note: use HTTP, not HTTPS**)
    1. Then click register.
1. In your newly created application
    1. On the Overview page, Note down for use later when configuring the example application:
        1. the Application (client) ID
        1. the Directory (tenant) ID
    1. Goto Manage
    1. Goto API Permissions
    1. Click Add Permission
        1. In the side pannel that appears, Click the tab `API's my organization uses`
        1. Search for `Power Platform API`.
            1. *If you do not see `Power Platform API` see the note at the bottom of this section.*
        1. In the permissions list choose `CopilotStudio` and Check `CopilotStudio.Copilots.Invoke`
        1. Click `Add Permissions`
    1. (Optional) Click `Grant Admin consent for copilotsdk`
    1. Close Azure Portal

> [!TIP]
> If you do not see `Power Platform API` in the list of API's your organization uses, you need to add the Power Platform API to your tenant. To do that, goto [Power Platform API Authentication](https://learn.microsoft.com/power-platform/admin/programmability-authentication-v2#step-2-configure-api-permissions) and follow the instructions on Step 2 to add the Power Platform Admin API to your Tenant

## Instructions - Configure the Example Application

With the above information, you can now run the client `CopilostStudioClientSample`.

1. Open the appSettings.json file for the CopilotStudioClientSample, or rename launchSettings.TEMPLATE.json to launchSettings.json.
1. Configured the values for the various key's based on what was recorded during the setup phase.

```json
  "DirectToEngineSettings": {
    "EnvironmentId": "", // Environment ID of environment with the CopilotStudio App.
    "BotIdentifier": "", // Schema Name of the Copilot to use
    "TenantId": "", // Tenant ID of the App Registration used to login,  this should be in the same tenant as the Copilot.
    "AppClientId": "" // App ID of the App Registration used to login,  this should be in the same tenant as the Copilot.
  }
```

3. Run the CopilotStudioClientSample.exe program.

This should challenge you for login and connect ot the Copilot Studio Hosted bot, allowing you to communicate via a console interface.

## Authentication

The DirectToEngine Client requires a User Token to operate. For this sample, we are using a user interactive flow to get the user token for the application ID created above. 

The Copilot client will use a named `HttpClient` retrieved from the `IHttpClientFactory` as `mcs` injected in DI. This client needs to be configured with a `DelegatingHandler` to apply a valid Entra ID Token. In this sample using MSAL.

