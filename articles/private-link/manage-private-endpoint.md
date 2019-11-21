---
title: Manage a Private Endpoint connection in Azure
description: Learn how to manage private endpoint connections in Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 929dfedbbbbe58a30eaa186398c595eaaabeb0a9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232535"
---
# <a name="manage-a-private-endpoint-connection"></a>Manage a Private Endpoint connection
Azure Private Link works on an approval call flow model wherein the Private Link service consumer can request a connection to the service provider for consuming the service. The service provider can then decide whether to allow the consumer to connect or not. Azure Private Link enables the service providers to manage the private endpoint connection on their resources. This article provides instructions about how to manage the Private Endpoint connections.

![Manage Private Endpoints](media/manage-private-endpoint/manage-private-endpoint.png)

There are two connection approval methods that a Private Link service consumer can choose from:
- **Automatic**: If the service consumer has RBAC permissions on the service provider resource, the consumer can choose the automatic approval method. In this case, when the request reaches the service provider resource, no action is required from the service provider and the connection is automatically approved. 
- **Manual**: On the contrary, if the service consumer doesn’t have RBAC permissions on the service provider resource, the consumer can choose the manual approval method. In this case, the connection request appears on the service resources as **Pending**. The service provider has to manually approve the request before connections can be established. In manual cases, service consumer can also specify a message with the request to provide more context to the service provider. The service provider has following options to choose from for all Private Endpoint connections: **Approved**, **Reject**, **Remove**.

The below table shows the various service provider actions and the resulting connection states for Private Endpoints.  The service provider can also change the connection state of private endpoint connection at a later time without consumer intervention. The action will update the state of the endpoint on the consumer side. 


|Service Provider Action   |Service Consumer Private Endpoint State   |Descrição   |
|---------|---------|---------|
|Nenhuma    |    Pendente     |    Connection is created manually and is pending for approval by the Private Link resource owner.       |
|Aprovar    |  Aprovado       |  Connection was automatically or manually approved and is ready to be used.     |
|Rejeitar     | Rejected        | Connection was rejected by the private link resource owner.        |
|Remover    |  Disconnected       | Connection was removed by the private link resource owner, the private endpoint becomes informative and should be deleted for clean up.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Manage Private Endpoint Connections on Azure PaaS resources
Portal is the preferred method for managing private endpoint connections on Azure PaaS resources. Currently, we don’t have PowerShell/CLI support for managing connections on Azure PaaS resources.
1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Navigate to Private Link Center.
3. Under **Resources**, select the resource type you want to manage the private endpoint connections.
4. For each of your resource type, you can view the number of Private Endpoint Connections associated with it. You can filter the resources as needed.
5. Select the private endpoint connections.  Under the connections listed, select the connection that you want to manage. 
6. You can change the state of the connection by selecting from the options at the top.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Manage Private Endpoint connections on a customer/partner owned Private Link service

Azure PowerShell and Azure CLI are the preferred methods for managing Private Endpoint connections on Microsoft Partner Services or customer owned services. Currently, we don’t have any portal support for managing connections on a Private Link service.  
 
### <a name="powershell"></a>PowerShell 
  
Use the following PowerShell commands to manage private endpoint connections.  
#### <a name="get-private-link-connection-states"></a>Get Private Link connection states 
Use the `Get-AzPrivateLinkService` cmdlet to get the Private Endpoint connections and their states.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Approve a Private Endpoint connection 
 
Use the `Approve-AzPrivateEndpointConnection` cmdlet to approve a Private Endpoint connection. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Deny Private Endpoint connection 
 
Use the `Deny-AzPrivateEndpointConnection` cmdlet to reject a Private Endpoint connection. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Remove Private Endpoint Connection 
 
Use the `Remove-AzPrivateEndpointConnection` cmdlet to remove a Private Endpoint connection. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>CLI do Azure 
 
Use `az network private-link-service update` for managing your Private Endpoint connections. The connection state is specified in the ```azurecli connection-status``` parameter. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Passos seguintes
- [Learn about Private Endpoints](private-endpoint-overview.md)
 
