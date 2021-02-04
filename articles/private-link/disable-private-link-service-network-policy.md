---
title: 'Desativar as políticas de rede para o endereço IP de fonte de serviço de ligação privada Azure '
description: Saiba como desativar as políticas de rede para o Azure Private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7528d12dcbfba09b4dae4ac07a5c683a7c11bd88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548806"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Desativar as políticas de rede para a fonte de serviço de Ligação Privada IP

Para escolher um endereço IP de origem para o seu serviço De Ligação Privada, é necessária uma definição de desativação explícita `privateLinkServiceNetworkPolicies` na sub-rede. Esta definição só é aplicável para o endereço IP privado específico que escolheu como IP de origem do serviço Private Link. Para outros recursos na sub-rede, o acesso é controlado com base na definição de regras de segurança dos Grupos de Segurança da Rede (NSG). 
 
Ao utilizar o portal para criar um serviço de Ligação Privada, esta definição é automaticamente desativada como parte do processo de criação. As implementações que utilizem qualquer cliente Azure (PowerShell, CLI ou modelos), requerem um passo adicional para alterar esta propriedade. Pode desativar a política utilizando a casca de nuvem a partir do portal Azure, ou instalações locais da Azure PowerShell, Azure CLI ou utilizar modelos de Gestor de Recursos Azure.  
 
Siga os passos abaixo para desativar as políticas de rede de serviços de ligação privada para uma rede virtual chamada *myVirtualNetwork* com uma sub-rede *padrão* hospedada num grupo de recursos chamado *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Esta secção descreve como desativar as políticas de ponto final privado da sub-rede utilizando a Azure PowerShell.
No código, substitua o "predefinido" pelo nome da sub-rede virtual.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Esta secção descreve como desativar as políticas de ponto final privado da sub-rede utilizando o Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Usando um modelo
Esta secção descreve como desativar as políticas de ponto final privado da sub-rede utilizando o Modelo de Gestor de Recursos Azure.
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
- Saiba mais sobre [o Azure Private Endpoint](private-endpoint-overview.md)
 
