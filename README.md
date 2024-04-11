******    Purpose    ******

This Repo Deploys an Application Insights Azure cloud Resource to Azure Cloud using GitHub ARM Template file and a Workflow YAML file.

##### Below is the Reference instruction and YAML file used



****** Reference Used    ******

Deploy ARM Template using GitHub
https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-github-actions?tabs=userlevel

Create a Service Principal for GitHub
	1.	az ad sp create-for-rbac --name "myML" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> --json-auth
	2.	Copy the output and paste in the main GitHub repository to be used as a value under Settings -> Security -> Actions -> New repository secret with name field as AZURE_CREDENTIALS

 TIPS: To create from scratch, first Deploy an app insights in Azure portal, then use the Deployment ARM Template to create your ARM Automation Template file!
 

****** YAML File Used    ******

on: [push]
  name: Azure ARM
  jobs:
    build-and-deploy:
      runs-on: ubuntu-latest
      steps:

        # Checkout code
      - uses: actions/checkout@main

        # Log into Azure
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

        # Deploy ARM template
      - name: Run ARM deploy
        uses: azure/arm-deploy@v1
        with:
          subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
          resourceGroupName: rsgrp-mkt-east-pr7ytrg
          template: ./application-insights-mkt-east.json
          parameters: workspaceResourceId=9410b8c3-1d9c-4c48-b3d5-9dc61afd4a68
                      requestSource=IbizaAIExtension
                      regionId=eastus
                      type=microsoft.insights/components
                      name=appInsightjsonARMDeploy
                      tagsArray='[{"Department":"Marketing","Environment":"Prod","Cost-Center":"CSTPR-MK543","Project":"EcoEnergyCampaign","Team":"IndivResAppDev"}]'
                      workspaceResourceId=/subscriptions/d8063b4c-5a26-4bb2-a00b-b40ab0c2ba66/resourcegroups/DefaultResourceGroup-EUS/providers/Microsoft.OperationalInsights/workspaces/DefaultWorkspace-d8063b4c-5a26-4bb2-a00b-b40ab0c2ba66-EUS

        # output containerName variable from template
      - run: echo ${{ steps.deploy.outputs.containerName }}
