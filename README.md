Steps to deploy **RocketMQ** as Kuberenetes Deployment with **Azure Disk** as data persistence.

1. Create an Azure Disk through portal or CLI or Powershell

2. Check AKS cluster identity by running below command. The System assigned or User Assigned Identity needs permission to bind to the Azure Disk

   ```
   az aks show -g <CLUSTER_RG> -n <CLUSTER_NAME> --query identity
   ``` 
