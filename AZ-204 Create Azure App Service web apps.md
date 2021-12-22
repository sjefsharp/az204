# AZ-204: Create Azure App Service web apps
## Explore Azure App Service
### Examine Azure App Service
Azure App Service is an HTTP-based service for hosting web appilcation, REST APIs, and monile back ends.
#### Built-in auto scale support
Baked into Azure App Service is the ability to scale up/down (RAM/CORES) or scale out/in (increase/decrease machine instances).
#### Continuous integration/deployment support
The Azure portal provides out-of-the-box continuous integration and deployment with Azure DevOps, GitHub, Bitbucket, FTP, or a local Git repository on your development machine. Connect your web app with any of the above sources and App Service will do the rest for you by auto-syncing code and any future changes on the code into the web appp.
#### Deployment slots
Using te Azure portal, or command-line tools, you caneasily add deployment slots to an App Service web app. You can create a staging deployment slot where you can push your code to test on Azure. You can easily swap the staging deployment slot with the production slot. A few simple mouse clicks in Azure.
#### App Service on Linux
App Service can also host web apps natively on Linux for supported application stacks. It can also run custom Linux containers (Web App for Containers). App Service on Linux supports a number of language specific built-in images. Supported languages: Node.js, Java, PHP, Python, .Net Core and Ruby. If the runtime is not supported, you can deploy it with a custom container.

The languages, and their supported versions, are update on a regular basis. You can retreive the current list by using the following command in the Cloud Shell.
```bash
az webapp list-runtimes --linux
```
#### Limitations
App Service on Linux does have some limitations:
- App Service on Linux is not supported on Shared pricing tier.
- You can't mix Windows and Linux apps in the same App Service plan.
- Historically, you could not mix Windows and Linux app in the same resource group. However, all resource groups created on or after January 21, 2021 do support this scenario. Support for resource groups created before January 21, 2021 will be rolled out across Azure regions.
- The Azure portal shows only features that currently work for Linux apps.
### Examine Azure App Service plans
In App Service, an app (Web Apps, API Apps, Mobile Apps or Azure Functions) always runs in an _App Service plan_. An App Service plan defines a set of compute resources for a web app to run. One or more apps can be configured to run on the same computing resources.

When you create an App Service plan in a certain region, as set of compute resources is created for that plan in that region. Each App Service plan defines:
- Region
- Number of VM instances
- Size of VM instances (Small, Medium, Large)
- Pricing tier (Free, Shard, Basis, Standard, Premium, PremiumV2, PremiumV3, Isolated)

The _pricing tier_ of an App Service plan determines what App Service features you get and how much you pay for the plan.
Pricing tier categories:
- **Shared compute**: Both **Free** and **Shared** share the resource pools of your apps with the apps of other customers. These tiers allocate CPU quotas to each app that runs on the shared resources, and resources can't scale out. These tiers are intended to be used only for development and testing purposes.
- **Dedicated compute**: The **Basic**, **Standard**, **Premium**, **PremiumV2**, and **PremiumV3** tiers run apps on dedicated Azure VMs. Only apps in the same App Service plan share the same compute resources. The higher the tier, the more VM instances are available to you for scale-out.
- **Isolated**: This tier runs dedicated Azure VMs on dedicated Azure Virtual Networks. It provides network isolation on top of compute isolation to your apps. It provieds maximum scale-out capabilities.
- **Consumption**: This tier isonly available to _function apps_. It scales the functions dynamicaly depending on workload.
#### How does my app run and scale?
In the **Free** and **Shared** tiers, an app recieves CPU minutes on a shared VM instance and can't scale out. In other tiers, an app runs and scales as follows:
- An app runs on all the VM instances configured in the App Service plan.
- If multiple apps are in the same App Service plan, they all share the same VM instances.
- If you have multiple deployments slots for an app, all deployments slots also run on the same VM instances.
- If you enable diagnostic logs, perform backups or run WebJobs, they also use CPU cycles and memory on these VM instances.
In this way, the App Service plan is the **scale unit* of the App Service app. If the plan is configured for autoscaling, then all apps in the plan are scaled out together based on autoscale settings.
#### What if my app needs more capabilities or features?
Your App Service plan can be scaled up and down at any time. It is as simple as changing the pricing tier of the plan.
You can potentially save money by putting multiple apps into one App Service plan. However, since apps in the same App Service plan all share the same compute resources you need to understand the capacity of the existing App Sewrvice plan and the expected load for the new app.

Isolate your app into a new App Service plan when:
- The app is resource-intensive.
- You want to scale the app independently from the other apps in the existing plan.
- The app needs resources in a different geographical region.
### Deploy to App Service
App Service supports both automated and manual deployment.
#### Automated deployment
Automated deployment or continuous integration, is a process used to push out new featured and bug fixes in a fast and repetitive patter with minimal impact on end users.
Azure supports automated deployment directly from several sources:
- **Azure DevOps**: You can push your code to Azure DevOps, build your code inthe cloud, run the tests, generate a release from the code, and finally, push your cot to an Azure Web App.
- **GitHub** Azure supports automated deployment directly from GitHub. When you connect your GitHub repository to Azure for automated deployments, any chacnes you push to your production branch on GitHub will be automatically deployed for you.
- **Bitbucket**: With its similarities to Github, you can configure automated deployment with Bitbucket.
#### Manual deployment
- **Git**: App Service web apps feature a Git URL that you can add as a remote repository. Pushing to the remote repository will deploy your app.
- **CLI**: ```az webapp up``` is a feature of the ```az``` command-line interface that packages your app and deploys it. Unlike other deployment methods, ```az webapp up``` can create a new App Service web app for you if you haven't already created one.
- **Zip deploy**: User ```curl``` or a similar HTTP utility to send a ZIP of your applicaiton files to App Service.
- **FTS/S**: Is a traditional way of pushing your code to many hosting environments, including App Service.
#### Use deployment slots
Whenever possible, use deployment slots when deploying a new production build. When using a Standard App Service Plan tier or better, you can deploy your app to a staging environment and then swap your staging and production slots. The swap operation warms up the necessary worker instances to match your production scale, thus eliminating downtime.
### Explore authentication and authorization in App Service
Azure App Service provides built-in authentication and authorization support, so you can sign in users and access data by writing minimal or no code in your web app, API, and mobile back end, and also Azure Functions.
#### Why use the built-in authentication?
You are not required to use App Service for authentication and authorisation. 

The built-in authtentication feature for App Service and Azure Functions can save you time and effort by providing out-of-the-box authentication with federated identity providers, allowing you to focus on the rest of your application.
-Azure App Service allows you to integrate a variety of auth capabilities into your web apps or API without implementing them yourself.
- It's built directly into the platform and doesn't require any particular language, SDK, security expertise, or even any code to utilize.
- You can integratie with multiple login providers.
#### Identity providers
App Service uses federated identity, in which a third-party identity provider manages the user identities and authentication flow for you. The following are available by default:
|Provider                   |Sign-in endpoint           |How-To guidance                       |
|---------------------------|---------------------------|--------------------------------------|
|Ms Identity Platform       |```/.auth/login/aad```     |App Service Ms Identity Platform login|
|Facebook                   |```/.auth/login/facebook```|App Service Facebook login            |
|Google                     |```/.auth/login/google```  |App Service Google login              |
|Twitter                    |```/.auth/login/twitter``` |App Service Twitter login             |

When you enable authentication and authorization with one of these providers, its sign-in endpoint is available for user authentication and for validation of authentication tokens from the provider.
#### How it works
The authentication and authorization module runs in the same sandbox as your application code. When it's enabled, every incoming HTTP request passes through it before being handled by your application code. This module handles several things for your app:
- Authenticates users with the specified provider
- validates, stores, and refreshes tokens
- Manages the authenticated session
- Injects identity information into request headers
The module runs separately from your application code and is configured using app settings. No SDKs, specific languages, or changes to your application code are required.

In Linux and containers the authentication and authorization module runs in a seperate container, isolated from your application code. Because it does not run in-process, no direct integration with specific language frameworks is possible.
#### Authentication flow
The authentication flow is the same for all providers, but differs depending on whether you want to sign in with the provider's SDK.
- Without provider SDK: The application delegates federated sign-in to App Service. This is typically the case with browser apps, which can present the provider's login page to the user. The server code manages the sign-in process, so it is also called _server-directed_ or _server flow_.
- With provider SDK: The application signs users in to the provider manually and then submits the authentication token to App Service for validation. This is typically the case with browser-less apps, which can't present the provider's sign-in page to the user. The application code manages the sign-in process, so it is also called _client-directed flow_ or _client flow_. This applies to REST APIs, Azure Functions, JavaScript browser clients, and native mobile apps that sign users in using the provider's SDK.

|Step|Without provider SDK|With provider SDK|
|-|-|-|
|Sign user in|Redirects client to ```/.auth/login/<provider>```.|Client code signs user in directly with provider's SDK and recieves an authentication token.|
|Post-authentication|Provider redirects client to ```/.auth/login/<provider/callback>```.|Client code posts token from provider to ```/.auth/login/<provider>/callback``` for validation.|
|Establish authenticated session|App Service adds authenticated cookie to response.|App Service returns its own authentication token to client code.|
|Serve authenticated content|Client includes authentication cookie in subsequent requests (automatically handled by browser).|Client code presents autentication token in ```X-ZUMO-AUTH``` header (automatically handled by Mobile Apps clients SDKs).

For client browsers, App Service can automatically direct all unauthenticated users to ```/.auth/login/<provider>```. You can also present users with one or more ```/.auth/login/<provider>``` links to sign in to your app using their provider of choice.
#### Authorization behavior
In the Azure portal, you can configure App Service with a number of behaviors when an incoming request is not authenticated.
- **ALlow unauthenticated requests**: This option defers authorization of unauthenticated traffic to your application code.
- **Require authentication**: This option will reject any unauthenticated traffic to your application. This rejection can be a redirect action to one of the configured identity providers. In these cases, a browser client is redirected to ```/.auth/login/<provider>``` for the provider you choose. If the anonymous request comes from a native mobile app, the returned response is an ```HTTP 401 Unauthorized```. You can also configure the rejection to be an ```HTTP 401 Unauthorized``` or ```HTTP 403 Forbidden``` for all requests.
### Discover App Service Networking features
By default, apps hosted in App Service are accessible directly through the internet and can reach only internet-hosted endpoints.
There are tow main deployment types for Azure App Service. The multitenant public service hosts App Service plans in the Free, Shared, Basic, Standard, Premium, PremiumV2, and PremiumV3 pricing SKUs. There is also the single-tenant App Service Environment (ASE) hosts Isolated SKU App Service plans directly on your Azure virtual network.
#### Multitenant App Service networking features
Azure App Service is a distributed system. The roles that handle incoming HTTP or HTTPS requests are called _front ends_. The roles that host the customer workload are called _workers_. All the roles in an App Service deployment exist in a multinenant nwetwork.
Instead of connecting the networks, you need features to handle the various aspects of application communication.
|Inbound features|Outbound features|
|-|-|
|App-assigned address|Hybrid Connections|
|Access restrictions|Gateway-required VNet Integration|
|Service endpoints|VNet Integration|
|Private endpoint| |

|Inbound use case|Feature|
|-|-|
|Support IP-based SSL needs for your app|App-assigned address|
|Support unshared dedicated inbound address for your app|App-assigned address|
|Restrict access to your app from a set of well-defined addresses|Access restrictions|
### Default networking behavior
The Free and Shared SKU plans host customer workloads on multitenant workers. The Basic and higher plans host customer workloads that are dedicated to only one App Service plan. If you scale out the worker, all the apps in that App Service plan will be replicated on a new worker for each instance in your App Service plan.
#### Outbound addressess
The worker VMs are broken down in large part by the App Service plans. When you change the VM family, you get a different set of outbound addresses.

There are a number of addresses that are used for outbound calls. The outbound addresses used by your app for making outbound calls are listed in the properties for your app. These address adre shared by all the apps running on the same worker VM family in the App Service deployment. If you want to see all the addresses that your app might use in a scalse unit, there's property called ```possibleOutboundAddresses``` that wil list them.

#### Find outbound IPs
To find the outbound IP addresses currently used by your app in the Azure portal, click **Properties** in you app's left-hand navigation.
You can find the same information by running the following command in the Cloud Shell. They are listed in the **Additional Outbound IP Addresses** field.
```bash
az webapp show \
  --resource-group <group_name> \
  --name <app_name> \
  --query outboundIpAddresses \
  --output tsv
  ```
To find all possible outbound IP addresses for your app, regardless of pricing tiers, run the following command in the Cloud Shell.
```bash
  az webapp show \
    --resource-group <group_name> \
    --name <app_name> \
    -- query possibleOutboundIpAddresses \
    --output tsv
```
### Exercise: Create a static HTML web app by using Azure Cloud Shell
#### Create the web app
The ```az webapp up``` command makes it easy to create and update web apps. When executed it performs the following actions:
- Create a default resource group.
- Create a default app service plan.
- Create an app with the specified name.
- Zip deploy files from the current working directory to the web app.
#### Clean up resources
If you no longer need the resources you created in this exercise you can delete the resource group using the ```az group delete```.
## Configure web app settings
### Configure application settings
In App Service, app settings are variables passed as environment variables to the applicaiton code. For Linux apps and custom containers, App Service passes app settings to the container using the --env flag to set the environment variable in the container.
Application settings can be accessesd by navigating to your app's management page and selecting **Configuration > Application Settings**.
For ASP.NET and ASP.NET Core developers, setting app settings in App Service are like setting the in ```<appSettings>``` in _Web.config_ or _appsettings.json_, but the values in App Service override the ones in _Web.config_ or _appsettings.json_.
App settings are always encrypted when stored (encrypted-at-rest).
#### Adding and editing settings
To add a new app settings, click **New application setting**.
In a default, or custom, Linux container any nested JSON key structure in t he app settings name like ```ApplicationInsights:InstrumentationKey``` needs to be configured in App Service as ```ApplicationInsights__InstrumentationKey``` for the key name.
#### Editing application settings in bulk
To add or edit app settings in bulk, click the **Advanced** edit button. When finished, click **Update**.
#### Configure connection strings
Adding and editing connection strings follow the same principles as other app settings and they can also be tied to deployment slots.

There is one case where you may want to use conneciton strings instead of app settings for non-.Net languages: certain Azure database types are backed up along with the app only if you configure a connection string for the databse in your App Service app.
### Configure general settings
In the **Configuration > General settings** you can configure some common settings for your app. Some settings require you to scale up to higher pricing tiers.
Below is a list of the currently available settings:
- **Stack settings**: The software stack to run the app, including the language and SDK versions.
- **Platform settings**: Lets you configure settings for the hosting platform, including:
  - **Bitness**: 32-bit or 64-bit
  - **WebSocket protocol**: For ASP.NET SignalR or socket.io, for example.
  - **Always On**: Keep the app loaded even when ther's no traffic. By default, **Always On** is not enabled and the app is unloaded after 20 minutes without any incoming requests.
  - **Managed pipeline version**: The IIS pipeline mode. Set it to **Classic** if you have legacy app that requires an older version of IIS.
  - **HTTP version**: Set to 2.0 to enable support for HTTPS/2 protocol..
  - **ARR affinity**: In a multi-instance deployment, ensure that the cloent is routed to the same instance for the life of the session. You can set this option to **Off** for stateless applications.
- **Debugging**: Enable remote debugging for ASP.NET, ASP.NET Core, or Node.js apps. Turns off automatically after 48 hours.
- **Incoming client certificates**: require client certificates in mutual authentication. TLS mututal authentication is used to restrict access to your app by enabling different types of authentication for it.
### Configure path mappings
In the **Configuration > Path mappings** section you can configure handler mappings, and virtual application and directory mappings. The **Path mappings** page will display different options based on the OS type.
#### Windows apps (uncontainerized)
Handler mappings let you add custom script processors to handle requests for specific file extensions. To add a custom handler, click **New handler**. Configure the handler as follows:
- **Extension**: The file extension you want to handle, such as _*.php_ or _handler.fcgi_.
- **Script processor**: The absolute path of the script porcessor to you. Requests to files that match the file extension are processed by the script processor. Use the path ```D:\home\site\wwwroot``` to refer to your app's root directory.
- **Arguments**: Optional command-line arguments for the script processor.

Each app has the default root path (```/```) mapped to ```D:\home\site\wwwroot```, where your code is deployed by default.
To configure virtual appications and directories, specify each virtual directory and its corresponding physical path relative to the website root (```D:\home```). To mark a virtual directory as a web application, clear the **Directory** checbox.
#### Linux and containerized apps
You can add custom storage for your containerized app. Containerized apps include all Linux apps and also the Windows and Linux custom containers running on App Service. Click **New Azure Storage Mount** and configure your custom storage as follows:
- **Name**: Display name
- **Configuration options**: Basic or Advanced
- **Storage accounts**: The storage account with the container you want.
- **Storage type**: **Azure Blobs** or **Azure files**. Windows container apps only support Azure files.
- **Storage container**: For basic configuration, the container you want.
- **Share name**: For advanced configuration, the file share name.
- **Access key**: For advanced configuration, the access key.
- **Mount path**: The absolute path in your container to mount the custom storage.
### Enable diagnostic logging
|Type|Platform|Location|Description|
|-|-|-|-|
|Application logging|Windows, Linux|App Service file system and/or Azure Storage blobs|Logs messages generated by your application. The messages can be generated by the web framework you choose, or from your application code directly using the standard logging pattern of your language. Each message is assigned one of the following categories: **Critical**, **Error**, **Warning**, **Info**, **Debug**, and **Trace**.|
|Web server logging|Windows|App Service file system or Azure Storage blobs|Raw HTTP request data in the W3C extended log file format. Each log meesage includes data like the HTTP method, resource URI, client IP, client port, user agent, response code, and so on.|
|Detailed error logging|Windows|App Service file system|Copies of the _.htm_ error pages that would have been sent to the client browser. For security reasons, detailed error pages shouldn't be sent to clients in production, but App Service can save the error page each time an application error occurs that has HTTP code 400 or greater.|
|Failed request tracing|Windows|App Service file system|Detailed tracing information on failed requests, including a trace of the IIS components used to process the request and the time taken in each component. One folder is generated for each failed request, which contains the XML log file, and the XSL stylesheets to view the log file with.|
|Deployment logging|Windows, Linux|App Service file system|Helps determine why a deployment failed. Deployment logging happens automatically and ther are no configurable settings for deployment logging.|
#### Enable application logging (Windows)
1. To enable application logging for Windows apps in the Azure portal, navigate to your app and slect **App Service logs**.
2. Select **On** fpr either **Application Logging (Filesystem)** or **Application Logging (Blob)**, or both. The **Filesystem** option is for temporay debugging purposes, and turns intself off in 12 hours. The **Blob** option is for long-term logging, and needs a blob storage container to write logs to.
3. You can also set the **Level** of details included in hte log as shown in the table below.
|Level|Included categories|
|-|-|
|Disabled|None|
|Error|Error, Critical|
|Warning|Warning, Error, Critical|
|Information|Info, Warning, Error, Critical|
|Verbose|Trace, Debug, Info, Warning, Error, Critical (all categories)|
4. When finished, select **Save**.
##### Enable application logging (Linux/Container)
1. In **Application logging**, select **File System**.
2. In **Quota (MB)**, specify the disk quota for the application logs. In **Retention Period (Days)**, set the number of days the logs should be retained.
3. When finished, select **Save**.
#### Enable web server logging
1. For **web server logging**, select **Storage** to store logs on blob storage, or **File System** to store logs on the App Service file system.
2. In **Retention Period (Days)**, set the number of days the logs should be retained.
3. When finished, select **Save**.
#### Add log messages in code
For example:
- ASP.NET application can use the ```System.Diagnostics.Trace```class to log information to the application diagnostics log. For example:
```C#
System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened.")
```
- By default, ASP.NET Core use the ```Microsoft.Extensions.Logging.AzureAppServices``` logging provider.
#### Stream logs
Before you stream logs in real time, enable the log type that you want. Any information written to files ending in .txt, .log, or .htm that are stored in the ```/LogFiles``` directory (```d:/home/logfiles```) is streamed by App Service.
- Azure portal - To stream logs in the Azure portal , navigate to your app and select **Log stream**.
- Azure CLI - To stream logs live in Cloud Shell, use the following command:
```bash
az webapp log tail --name <appname> --resource-group <myResourceGroup>
```
- Local console - To stream logs in the local console, install Azure CLI and sign in to your account. Once signed in, follow the instruction for Azure CLI above.
#### Access log files
If you configure the Azure Storage blobs option for a log type, you need a client tool that works with Azure Storage.
For logs stored in the App Service file system, the easiest way is to download the ZIP file in the browser at:
- Linux/Container apps: ```https://<app-name>.scm.azurewebsites.net/api/log/docker/zip```
- Windows apps: ```https://<app-name>.scm.azurewebsites.net/api/dump```
## Configure security certificates
Azure App Service has tools that let you create, upload, or import a private certificate or a public certificate into App Service.

A certificate uploaded into an app is stored in a deployment unit that is bound to the app service plan's  resource group and region combination (internally called a _webspace_). This makes the certificate accessible to other apps in the same resource group and region combination.

The table below details the options you have for adding certificaties in App Service:
|Option|Description|
|-|-|
|Create a free App Service managed certificate|A private certificate that's free of charge and easy to use if you just need to secure your custom domain in App Service.|
|Purchase an App Service certificate|A private certificate that's managed by Azure. It combines the simplicity of automated certificate management and the flexibility of renewal and export options.
|Import a certificate from Key Vault|Useful if you use Azure Key Vault to manage your certificates.|
|Upload a private certificate|If you already have a private certificate from a third-party provider, you can upload it.|
|Upload a public certificate|Public certificates are not used to secure custom domains, but you can load them into your code if you need them to access remote resources.|
### Private certificate requirements
The free **App Service managed certificate** and the **App Service certificate** already satisfy the requirements of App Service. If you want to use a private certificate in App Service, your certificate must meet the following requirements:
- Exported as a password-protected PFX file, encrypted using triple DES.
- Contains private key at lease 2048 bits long.
- Contains all intermediate certificates in the certificate chain.
To secure a custom domain in a TLS binding, the certificate has addtional requirements:
- Contains an Extended Key Usage for server authentication (OID = 1.3.6.5.5.7.3.1)
- Signed by a trusted certificate authority.
### Creating a free managed certificate
To create custom TLS/SSL bindings or enable client certificates for your App Service app, your App Service plan must be in the **Basic**, **Standard**, **Premium**, or **Isolated** tier. Custom SSL is not supported in the **F1** or **D1** tier.
The free App Service managed certificate is a turn-key solution for securing your custom DNS name in App Service. It's a TLS/SSL server certificate that's fully managed by App Services and renewed continuously and automatically in six-moths incremetns, 45 days before expiration. You create the certificate and bind it to a custom domain, and let App Service do the rest.
The free certificate comes with the following limitations:
- Does not support wildcard certificates.
- Does not support usage as a client certificate by certificate thumbprint.
- Is not exportable.
- Is not supported on App Service Environment (ASE).
- Is not supported with root domains that are integrated with Traffic Manager.
If a certificate is for a CNAME-mapped domain, the CNAME must be mapped directly to ```<app-name>.azurewebsites.net```
### Import an App Service Certificate
If you purchase an App Service Certificate from Azure, Azure manages the following tasks:
- Takes care of the purchase process from GoDaddy.
- Performs domain verification on the certificate.
- Maintains the certificate in Azure Key Vault.
- Manages certificate renewal.
- Synchronize the certificate automatically with the imported copies in App Service apps.
If you already hva a working App Service certificate, you can:
- Import the certificate into App Service.
- Manage the certificate, such as renew, rekey, and export it.
### Upload a private certificate
If your certificate authority gives you multiple certificates in the certificate chain, you need to merge the certificates in order. Then you can Export your merged TLS/SSL certificate with the private key that your certificate request was generated with.

If you generated your certificate request using OpenSSL, then you have created a private key file. To export your certificate to PFX, run the following command. Replace the placeholders ```<private-key-file>``` and ```<merged-certificate-file>``` with the paths to your private key and your merged certicate file.
```bash
openssl pckcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certicate-file>
```
When prompted, define an export password. You'll use this password when uploading your TLS/SSL certifcate to App Service.
### Enforce HTTPS
By default, anyone can still access your app using HTTP. You can redirect all HTTP requests t the HTTPS port by navigating to your app page and, select **TLS/SSL settings**. Then, in **HTTPS Only**, select **On**.
## Manage app features
Feature management is a modern software-development practice that decouples feature release from code deployment and enables quick changes to feature availability on demand. It uses a technique called feature falgs to dynamically administer a feature's lifecycle.
### Basic concepts
Here are several new terms related to feature management:
- **Feature flag**: A feature flag is a variable with a binary state of _on_ or _off_. The fature flag also has an associated code block. The state of the feature flag triggers whether the code block runs or not.
- **Feature manager**: A feature manager is an application package that handles the lifecycle of all the feature flags and update their states.
- **Filter**: A filter is a rule for evaluating the state of a feature flag. A user group, a device or browser type, a geographic location, and a time window are all examples of what a filter can represent.

An effective implementation of feature management consists of at least two components working in concert:
- An applicaiton that makes use of feature flags.
- A separate repository that stores the feature flags and their current state.
### Feature flag usage in code
The basic patter for implementing feature flags in an application is simple. You can think of afeature flag as a Booelean state variable used with an ```if``` conditional statement in your code:
```C#
if (featureFlag) {
  // Run the following code
}
```
In this case, if ```featureFlag``` is set to ```True```, the enclosed code block is executed; otherwise, it's skipped. You can set the value of ```featureFlag``` statically, as in the following code example:
```C#
bool featureFlag = true;
```
You can also evaluate the flag's state based on certain rules:
```C#
bool featureFlage = isBetaUser();
```
A slightly more complicated feature flag pattern includes an ```else``` statement as well:
```C#
if (featureFlag) {
  // This following code will run if the featureFlage value is true
} else {
  // This following code will run if the featureFlag value is false
}
```
### Feature flag declaration
Each feature flag has two parts: a name and a list of one or more filters that are used to evaluate if a feature's state is _on_. A filter defines a use case for when na feature should be turned on.

When a feature flag has multiple filters, the filter list is traversed in order until one of the filters determines the feature should be enabled. A that point, the feature flag is _on_, and any remaining filter results are skipped. If no filter indicates the feature should be enabled, the feature flag is _off_.
The feature manager supports _appsettings.json_ as a configuration source for feature flags. The following example shows how to set up feature flags in a JSON file:
```JSON
"FeatureManagement": {
  "FeatureA": true, // Feature flag set to on
  "FeatureB": false, // Feature flag set to off
  "FeatureC": {
    "EnabledFor": [
      {
        "Name": "Percentage",
        "Parameters": {
          "Value": 50
        }
      }
    ]
  }
}
```
### Feature flag repository
To use feature flags effectively, you need to externalize all the feature flags used in an application. This approach allows you to change feature flag states without modifying and redeploying the application itself.

Azure App Configuration is designed to be a centralized repository for feature flags. You can use it to define different kinds of feature flags and manipulate theri states quickly and confidently. You can then use the App Configuration libraries for various programming language frameworks to easily access these feature flags from your application.