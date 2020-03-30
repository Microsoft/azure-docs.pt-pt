---
title: Desativar as políticas de rede para pontos finais privados em Azure
description: Aprenda a desativar as políticas de rede para pontos finais privados.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453024"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Desativar as políticas de rede para pontos finais privados

As políticas de rede como os grupos de segurança da rede (NSG) não são apoiadas para pontos finais privados. Para implantar um Ponto Final Privado numa determinada sub-rede, é necessária uma definição explícita de desativação nessa sub-rede. Esta definição só é aplicável para o Ponto Final Privado. Para outros recursos na subrede, o acesso é controlado com base na definição de regras de segurança dos Grupos de Segurança da Rede (NSG). 
 
Ao utilizar o portal para criar um ponto final privado, esta definição é automaticamente desativada como parte do processo de criação. A implementação utilizando outros clientes requer um passo adicional para alterar esta definição. Pode desativar a definição utilizando a casca de nuvem do portal Azure, ou instalações locais de Azure PowerShell, Azure CLI ou utilizar modelos do Gestor de Recursos Azure.  
 
Os exemplos seguintes `PrivateEndpointNetworkPolicies` descrevem como desativar uma rede virtual chamada *myVirtualNetwork* com uma subnet *padrão* hospedada num grupo de recursos chamado *myResourceGroup*.

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Esta secção descreve como desativar as políticas de ponto final privado da subnet utilizando o Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Esta secção descreve como desativar as políticas de pontofinal privado da sub-rede utilizando o Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o ponto final privado do Azure](private-endpoint-overview.md)
 
