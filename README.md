# grafana-aci-quick-start
GitHub Actions quick start workflow to build Grafana on Azure

This GitHub Actions workflow will help you get started on your data visualization and dashboard journey with Grafana. It makes use of several Azure services to get a serveless sandbox up and running quickly - about 10 minutes. 

Components Used:
- [GitHub Actions](https://github.com/features/actions)
  - [Ubuntu Latest Hosted Runner](https://docs.github.com/en/actions/reference/virtual-environments-for-github-hosted-runners)
  - Bash Shell
  - [Azure Login Action](https://github.com/marketplace/actions/azure-login)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure MySQL Database](https://azure.microsoft.com/en-us/services/mysql/)
- [Azure Container Instances](https://azure.microsoft.com/en-us/services/container-instances/)
- [Grafana Latest Docker Image](https://hub.docker.com/r/grafana/grafana)


# Grafana

[Grafana](https://grafana.com) is an open source data visualization and dashboard platform developed in 2014 by Torkel Ödegaard so that he had a better way to visualize time series data across multiple data platforms. Fast forward 6 years and Grafana is now in its 7.0 release supporting over 30 data platforms with over 400 instances globally. It's easily accessible from laptops, tablets, and mobile devices which means every one of your stakeholders can see the same data instantly. 


I've implemented this in the enterprise and have seen the following benefits:
- Easy to learn
- Works with traditional data stores like MySQL, MS SQL
- Native connectivity to Azure, AWS, GCP data stores
- Low Cost
- Portable dashboards on any device 

# Workflow Details

PreRecs:
- You need to create 2 GitHub Repository Secrets in your repo
  - Create a new App Registration (command below) and grants it the Contributor role to your subscription and place the JSON output into a secret called AZURE_CREDENTIALS. 
  
    `az ad sp create-for-rbac --name "GitHubDeploymentAcct" --role contributor --sdk-auth`
  - Set a password that will be used for the MySQL admin credentials and place it in a secret named MYSQLADMIN_PASSWORD. 

Execution:
- This workflow is configured to use a manual trigger. You can execute it by going to the Actions tab in GitHub then clicking the action. Click the button 'Run workflow'.
- The workflow has several inputs with default value required to run. There are specific notes about several item which must be unique and you are required to update. 

Results:
- The workflow will produce the following assets which are appropriate for a working Grafana sandbox
  - New Azure Resource Group
  - New Azure MySQL Database Instance
  - New MySQL Database for Grafana
  - New Azure Container Instance using the 'grafana/grafana' Docker image that is configured to 
    - Use the MySQL database provisioned
    - Have a public facing URL at the FQDN provided running on port 3000 (example -> https://FQDN:3000)
    - Self-Signed SSL encryption certificate is installed with 1 year expiration 

# Considerations
PLEASE make sure you have a plan when moving out of your sandbox. These are items that I've addressed and have experience deploying for a true enterprise-ready solution. So take my word you need to consider these! 
- The current config applies a self-signed SSL encryption certificate which means there is encryption in transit yet not with a trusted certificate authority. You'll receive a security warning in your browser that you will have to accept. This is fine for a sandbox yet not for production use.
- Additional credentials should be created for the MySQL host so that least privilege access can be maintained. 
- The capacity settings for compute, memory, and storage are set to bare minimum. Validate which SKU's are the right fit. Grafana is very reasonable on resource usage so don't go crazy. 
- Grafana supports SAML/OAuth/etc, you should consider leveraging your existing enterprise directory/auth platform. 
- Grafana scales out well. You'd need to consider using additional Azure features to address load balancing and orchestration. 


#Grafana #Azure
