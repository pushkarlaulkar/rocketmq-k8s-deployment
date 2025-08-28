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

   Output should be similar to below

   ```
   {
     "condition": null,
     "conditionVersion": null,
     "createdBy": null,
     "createdOn": "2025-08-28T13:00:31.660292+00:00",
     "delegatedManagedIdentityResourceId": null,
     "description": null,
     "id": "/subscriptions/<SUB_ID>/resourceGroups/<Resource_Group>/providers/Microsoft.Authorization/roleAssignments/role-id",
     "name": "role-id",
     "principalId": "your-principal-id",
     "principalType": "ServicePrincipal",
     "resourceGroup": "<Resource_Group>",
     "roleDefinitionId": "/subscriptions/<SUB_ID>/providers/Microsoft.Authorization/roleDefinitions/role-definition-id",
     "scope": "/subscriptions/<SUB_ID>/resourceGroups/<Resource_Group>",
     "type": "Microsoft.Authorization/roleAssignments",
     "updatedBy": "updated-by-id",
     "updatedOn": "2025-08-28T13:00:32.054293+00:00"
   }
   ```

4. Create PV by applying below command

   ```
   kubectl apply -f pv.yaml
   ```

5. Create PVC

   ```
   kubectl apply -f pvc.yaml
   ```

6. Make sure PVC is bound to the PV. Output should be similar as below

   ```
   NAME                            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                          STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE    VOLUMEMODE
   persistentvolume/rmqbroker-pv   256Gi      RWO            Retain           Bound    your-namespace/rmqbroker-pvc                  <unset>                          6h9m   Filesystem
   
   NAME                                  STATUS   VOLUME         CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE     VOLUMEMODE
   persistentvolumeclaim/rmqbroker-pvc   Bound    rmqbroker-pv   256Gi      RWO                           <unset>                 5h50m   Filesystem
   ```

7. Finally apply the deployment

   ```
   kubectl apply -f rocketmq.yaml
   ```
