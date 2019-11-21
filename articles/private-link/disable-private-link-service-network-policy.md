---
title: 'Disable network policies for Azure Private Link service source IP address '
description: Learn how to disable network policies for Azure private Link
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 22dbb12de0793db8aac12f610ff94380542d426a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228109"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Disable network policies for Private Link service source IP

In order to choose a source IP address for your Private Link service, an explicit disable setting `privateLinkServiceNetworkPolicies` is required on the subnet. This setting is only applicable for the specific private IP address you chose as the source IP of the Private Link service. For other resources in the subnet, access is controlled based on Network Security Groups (NSG) security rules definition. 
 
When using any Azure client (PowerShell, CLI or templates), an additional step is required to change this property. You can disable the policy using the cloud shell from the Azure portal, or local installations of Azure PowerShell, Azure CLI, or use Azure Resource Manager templates.  
 
Follow the steps below to disable private link service network policies for a virtual network named *myVirtualNetwork* with a *default* subnet hosted in a resource group named *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
This section describes how to disable subnet private endpoint policies using Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
This section describes how to disable subnet private endpoint policies using Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Using a template
This section describes how to disable subnet private endpoint policies using Azure Resource Manager Template.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Passos seguintes
- Learn more about [Azure Private Endpoint](private-endpoint-overview.md)
 
