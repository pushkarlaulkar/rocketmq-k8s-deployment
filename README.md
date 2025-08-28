Steps to deploy **RocketMQ** as Kuberenetes Deployment with **Azure Disk** as data persistence.

1. Create an Azure Disk through portal or CLI or Powershell

2. Check AKS cluster identity by running below command. The System assigned or User Assigned Identity needs permission to bind to the Azure Disk

   ```
   az aks show -g <CLUSTER_RG> -n <CLUSTER_NAME> --query identity
   ```
   Output should be similar to below

   ```
   {
     "delegatedResources": null,
     "principalId": "your-principal-id",
     "tenantId": "your-tenant-id",
     "type": "SystemAssigned",
     "userAssignedIdentities": null
   }
   ```

3. Grant Contributor Role to the principal id by running below command

   ```
   az role assignment create \
   --assignee your-principal-id \
   --role "Contributor" \
   --scope /subscriptions/<SUB_ID>/resourceGroups/<Resource_Group>
   ```
