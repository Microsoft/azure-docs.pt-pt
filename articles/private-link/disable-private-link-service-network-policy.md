---
title: 'Desabilitar políticas de rede para o endereço IP de origem do serviço de vínculo privado do Azure '
description: Saiba como desabilitar as políticas de rede para o link privado do Azure
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: c7a0968d32418867d3e929786a42da9349806b2d
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018938"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Desabilitar políticas de rede para o IP de origem do serviço de vínculo privado

Para escolher um endereço IP de origem para seu serviço de vínculo privado, uma configuração `privateLinkServiceNetworkPolicies` de desabilitação explícita é necessária na sub-rede. Essa configuração só é aplicável para o endereço IP privado específico que você escolheu como o IP de origem do serviço de link privado. Para outros recursos na sub-rede, o acesso é controlado com base na definição de regras de segurança de NSG (grupos de segurança de rede). 
 
Ao usar qualquer cliente do Azure (PowerShell, CLI ou modelos), é necessária uma etapa adicional para alterar essa propriedade. Você pode desabilitar a política usando o Cloud shell do portal do Azure, ou instalações locais do Azure PowerShell, CLI do Azure ou usar modelos de Azure Resource Manager.  
 
Siga as etapas abaixo para desabilitar as políticas de rede do serviço de vínculo privado para uma rede virtual denominada *myVirtualNetwork* com uma sub-rede *padrão* hospedada em um grupo de recursos chamado *MyResource*Group. 

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Esta seção descreve como desabilitar políticas de ponto de extremidade privadas de sub-rede usando Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Esta seção descreve como desabilitar políticas de ponto de extremidade privadas de sub-rede usando CLI do Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Usando um modelo
Esta seção descreve como desabilitar políticas de ponto de extremidade privadas de sub-rede usando Azure Resource Manager modelo.
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
## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [ponto de extremidade privado do Azure](private-endpoint-overview.md)
 
