```bash
az webapp list-runtimes --linux #lists available runtimes on Web App.
az webapp up #packages your app and deploys it, creates a new Web App if none exists.
az webapp show --resource-group <group_name> --name <app_name> --query outbountIpAddresses --output tsv #find outbound IP addresses currently used by your app in the Azure Portal.
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv #find all possible outbount IP addresses for your app.
```
