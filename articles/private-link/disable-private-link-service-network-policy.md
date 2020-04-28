---
title: 'Desativar as políticas de rede para o endereço IP de origem de serviço seletivo do Link Privado Azure '
description: Saiba como desativar as políticas de rede para o Azure private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75452998"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Desativar as políticas de rede para a fonte de serviço de Link Privado IP

Para escolher um endereço IP de origem para o `privateLinkServiceNetworkPolicies` seu serviço de Ligação Privada, é necessária uma definição explícita de desativação na sub-rede. Esta definição só é aplicável para o endereço IP privado específico que escolheu como o IP fonte do serviço Private Link. Para outros recursos na subrede, o acesso é controlado com base na definição de regras de segurança dos Grupos de Segurança da Rede (NSG). 
 
Ao utilizar qualquer cliente Azure (PowerShell, CLI ou modelos), é necessário um passo adicional para alterar esta propriedade. Pode desativar a apólice utilizando a casca de nuvem do portal Azure, ou instalações locais de Azure PowerShell, Azure CLI ou utilizar modelos do Gestor de Recursos Azure.  
 
Siga os passos abaixo para desativar as políticas de rede de serviços de ligação privada para uma rede virtual chamada *myVirtualNetwork* com uma subnet *padrão* hospedada num grupo de recursos chamado *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Esta secção descreve como desativar as políticas de ponto final privado da subnet utilizando o Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Esta secção descreve como desativar as políticas de pontofinal privado da sub-rede utilizando o Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Usando um modelo
Esta secção descreve como desativar as políticas de ponto final privado da subnet utilizando o modelo de gestor de recursos azure.
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
 
