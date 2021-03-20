---
title: Desativar as políticas de rede para pontos finais privados em Azure
description: Saiba como desativar as políticas de rede para pontos finais privados.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737517"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Desativar as políticas de rede para pontos finais privados

As políticas de rede como os grupos de segurança de rede (NSG) não são suportadas para pontos finais privados. Para implantar um Ponto Final Privado numa determinada sub-rede, é necessária uma definição explícita de desativação nessa sub-rede. Esta definição só é aplicável para o Ponto Final Privado. Para outros recursos na sub-rede, o acesso é controlado com base na definição de regras de segurança dos Grupos de Segurança da Rede (NSG). 
 
Ao utilizar o portal para criar um ponto final privado, esta definição é automaticamente desativada como parte do processo de criação. A implementação utilizando outros clientes requer um passo adicional para alterar esta definição. Pode desativar a definição utilizando a casca de nuvem a partir do portal Azure, ou instalações locais da Azure PowerShell, Azure CLI ou utilizar modelos de Gestor de Recursos Azure.  
 
Os exemplos a seguir descrevem como desativar `PrivateEndpointNetworkPolicies` uma rede virtual chamada *myVirtualNetwork* com uma sub-rede *padrão* hospedada num grupo de recursos chamado *myResourceGroup*.

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Esta secção descreve como desativar as políticas de ponto final privado da sub-rede utilizando a Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Esta secção descreve como desativar as políticas de ponto final privado da sub-rede utilizando o Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o ponto final privado do Azure](private-endpoint-overview.md)
 
