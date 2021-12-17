# AZ-204: Create Azure App Service web apps
## Examine Azure App Service
Azure App Service is an HTTP-based service for hosting web appilcation, REST APIs, and monile back ends.
### Built-in auto scale support
Baked into Azure App Service is the ability to scale up/down (RAM/CORES) or scale out/in (increase/decrease machine instances).
### Continuous integration/deployment support
The Azure portal provides out-of-the-box continuous integration and deployment with Azure DevOps, GitHub, Bitbucket, FTP, or a local Git repository on your development machine. Connect your web app with any of the above sources and App Service will do the rest for you by auto-syncing code and any future changes on the code into the web appp.
### Deployment slots
Using te Azure portal, or command-line tools, you caneasily add deployment slots to an App Service web app. You can create a staging deployment slot where you can push your code to test on Azure. You can easily swap the staging deployment slot with the production slot. A few simple mouse clicks in Azure.
### App Service on Linux
App Service can also host web apps natively on Linux for supported application stacks. It can also run custom Linux containers (Web App for Containers). App Service on Linux supports a number of language specific built-in images. Supported languages: Node.js, Java, PHP, Python, .Net Core and Ruby. If the runtime is not supported, you can deploy it with a custom container.

The languages, and their supported versions, are update on a regular basis. You can retreive the current list by using the following command in the Cloud Shell.
```bash
az webapp list-runtimes --linux
```
### Limitations
App Service on Linux does have some limitations:
- App Service on Linux is not supported on Shared pricing tier.
- You can't mix Windows and Linux apps in the same App Service plan.
- Historically, you could not mix Windows and Linux app in the same resource group. However, all resource groups created on or after January 21, 2021 do support this scenario. Support for resource groups created before January 21, 2021 will be rolled out across Azure regions.
- The Azure portal shows only features that currently work for Linux apps.
## Examine Azure App Service plans
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
### How does my app run and scale?
In the **Free** and **Shared** tiers, an app recieves CPU minutes on a shared VM instance and can't scale out. In other tiers, an app runs and scales as follows:
- An app runs on all the VM instances configured in the App Service plan.
- If multiple apps are in the same App Service plan, they all share the same VM instances.
- If you have multiple deployments slots for an app, all deployments slots also run on the same VM instances.
- If you enable diagnostic logs, perform backups or run WebJobs, they also use CPU cycles and memory on these VM instances.
In this way, the App Service plan is the **scale unit* of the App Service app. If the plan is configured for autoscaling, then all apps in the plan are scaled out together based on autoscale settings.
### What if my app needs more capabilities or features?
Your App Service plan can be scaled up and down at any time. It is as simple as changing the pricing tier of the plan.
You can potentially save money by putting multiple apps into one App Service plan. However, since apps in the same App Service plan all share the same compute resources you need to understand the capacity of the existing App Sewrvice plan and the expected load for the new app.

Isolate your app into a new App Service plan when:
- The app is resource-intensive.
- You want to scale the app independently from the other apps in the existing plan.
- The app needs resources in a different geographical region.
## Deploy to App Service
App Service supports both automated and manual deployment.
### Automated deployment
Automated deployment or continuous integration, is a process used to push out new featured and bug fixes in a fast and repetitive patter with minimal impact on end users.
Azure supports automated deployment directly from several sources:
- **Azure DevOps**: You can push your code to Azure DevOps, build your code inthe cloud, run the tests, generate a release from the code, and finally, push your cot to an Azure Web App.
- **GitHub** Azure supports automated deployment directly from GitHub. When you connect your GitHub repository to Azure for automated deployments, any chacnes you push to your production branch on GitHub will be automatically deployed for you.
- **Bitbucket**: With its similarities to Github, you can configure automated deployment with Bitbucket.
### Manual deployment
- **Git**: App Service web apps feature a Git URL that you can add as a remote repository. Pushing to the remote repository will deploy your app.
- **CLI**: ```az webapp up``` is a feature of the ```az``` command-line interface that packages your app and deploys it. Unlike other deployment methods, ```az webapp up``` can create a new App Service web app for you if you haven't already created one.
- **Zip deploy**: User ```curl``` or a similar HTTP utility to send a ZIP of your applicaiton files to App Service.
- **FTS/S**: Is a traditional way of pushing your code to many hosting environments, including App Service.
### Use deployment slots
Whenever possible, use deployment slots when deploying a new production build. When using a Standard App Service Plan tier or better, you can deploy your app to a staging environment and then swap your staging and production slots. The swap operation warms up the necessary worker instances to match your production scale, thus eliminating downtime.
## Explore authentication and authorization in App Service
Azure App Service provides built-in authentication and authorization support, so you can sign in users and access data by writing minimal or no code in your web app, API, and mobile back end, and also Azure Functions.
### Why use the built-in authentication?
You are not required to use App Service for authentication and authorisation. 

The built-in authtentication feature for App Service and Azure Functions can save you time and effort by providing out-of-the-box authentication with federated identity providers, allowing you to focus on the rest of your application.
-Azure App Service allows you to integrate a variety of auth capabilities into your web apps or API without implementing them yourself.
- It's built directly into the platform and doesn't require any particular language, SDK, security expertise, or even any code to utilize.
- You can integratie with multiple login providers.
### Identity providers
App Service uses federated identity, in which a third-party identity provider manages the user identities and authentication flow for you. The following are available by default:
|Provider                   |Sign-in endpoint           |How-To guidance                       |
|---------------------------|---------------------------|--------------------------------------|
|Ms Identity Platform       |```/.auth/login/aad```     |App Service Ms Identity Platform login|
|Facebook                   |```/.auth/login/facebook```|App Service Facebook login            |
|Google                     |```/.auth/login/google```  |App Service Google login              |
|Twitter                    |```/.auth/login/twitter``` |App Service Twitter login             |

When you enable authentication and authorization with one of these providers, its sign-in endpoint is available for user authentication and for validation of authentication tokens from the provider.
### How it works
The authentication and authorization module runs in the same sandbox as your application code. When it's enabled, every incoming HTTP request passes through it before being handled by your application code. This module handles several things for your app:
- Authenticates users with the specified provider
- validates, stores, and refreshes tokens
- Manages the authenticated session
- Injects identity information into request headers
The module runs separately from your application code and is configured using app settings. No SDKs, specific languages, or changes to your application code are required.

In Linux and containers the authentication and authorization module runs in a seperate container, isolated from your application code. Because it does not run in-process, no direct integration with specific language frameworks is possible.
### Authentication flow
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
### Authorization behavior
In the Azure portal, you can configure App Service with a number of behaviors when an incoming request is not authenticated.
- **ALlow unauthenticated requests**: This option defers authorization of unauthenticated traffic to your application code.
- **Require authentication**: This option will reject any unauthenticated traffic to your application. This rejection can be a redirect action to one of the configured identity providers. In these cases, a browser client is redirected to ```/.auth/login/<provider>``` for the provider you choose. If the anonymous request comes from a native mobile app, the returned response is an ```HTTP 401 Unauthorized```. You can also configure the rejection to be an ```HTTP 401 Unauthorized``` or ```HTTP 403 Forbidden``` for all requests.
## Discover App Service Networking features
By default, apps hosted in App Service are accessible directly through the internet and can reach only internet-hosted endpoints.
There are tow main deployment types for Azure App Service. The multitenant public service hosts App Service plans in the Free, Shared, Basic, Standard, Premium, PremiumV2, and PremiumV3 pricing SKUs. There is also the single-tenant App Service Environment (ASE) hosts Isolated SKU App Service plans directly on your Azure virtual network.
### Multitenant App Service networking features
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
## Default networking behavior
The Free and Shared SKU plans host customer workloads on multitenant workers. The Basic and higher plans host customer workloads that are dedicated to only one App Service plan. If you scale out the worker, all the apps in that App Service plan will be replicated on a new worker for each instance in your App Service plan.
### Outbound addressess

