```bash
az webapp list-runtimes --linux #lists available runtimes on Web App.
az webapp up #packages your app and deploys it, creates a new Web App if none exists.
az webapp show --resource-group <group_name> --name <app_name> --query outbountIpAddresses --output tsv #find outbound IP addresses currently used by your app in the Azure Portal.
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv #find all possible outbount IP addresses for your app.
az webapp up --location <myLocation> --name <myAppName> --html #Creates an app in a specified location with a specified name. The html flag tells az webapp up to createa a static html app.
code #Opens Visual Studio code either a new file, directory or existing file.
az group delete --name <resource_group> --no-wait #Deletes resource group. The --no-wait flag indicates asynchronous operation.
az webapp log tail --name <appname> --resource-group <myResourceGroup> #To stream logs live in Cloud Shell or locally using the azure cli.
openssl pckcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certicate-file> #Export a private certificate to a file.
```
