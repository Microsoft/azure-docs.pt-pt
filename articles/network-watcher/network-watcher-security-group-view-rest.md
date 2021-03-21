---
title: Analisar segurança de rede - Vista do Grupo de Segurança - Azure REST API
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como a API Azure REST para analisar uma segurança de máquinas virtuais com a Vista do Grupo de Segurança.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960609"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analise a segurança da sua máquina virtual com vista de grupo de segurança usando a API REST

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

> [!NOTE]
> A API do Grupo de Segurança já não está a ser mantida e será depreciada em breve. Utilize a [funcionalidade Regras de Segurança Eficazes](./network-watcher-security-group-view-overview.md) que fornece a mesma funcionalidade. 

A visão do grupo de segurança devolve regras de segurança configuradas e eficazes de segurança de rede que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar grupos de segurança de rede e regras que são configuradas num VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos como recuperar as regras de segurança eficazes e aplicadas a uma máquina virtual usando REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, ligue para a API do Observador de Rede para obter a visão do grupo de segurança para uma máquina virtual. A ARMclient é utilizada para chamar a API REST usando o PowerShell. ARMClient é encontrado em chocolate no [ARMClient em Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já seguiu os passos na [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede. O cenário pressupõe ainda que exista um Grupo de Recursos com uma máquina virtual válida.

## <a name="scenario"></a>Scenario

O cenário abordado neste artigo recupera as regras de segurança eficazes e aplicadas para uma determinada máquina virtual.

## <a name="log-in-with-armclient"></a>Iniciar sessão com ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperar uma máquina virtual

Executar o seguinte script para devolver uma máquina virtual O seguinte código precisa de variáveis:

- **subscriçãoId** - O id de subscrição também pode ser recuperado com o **cmdlet Get-AzSubscription.**
- **resourceGroupName** - O nome de um grupo de recursos que contém máquinas virtuais.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A informação necessária é a **id** sob o tipo `Microsoft.Compute/virtualMachines` de resposta, como se pode ver no exemplo seguinte:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Obtenha vista de grupo de segurança para máquina virtual

O exemplo a seguir solicita a visão do grupo de segurança de uma máquina virtual direcionada. Os resultados deste exemplo podem ser usados para comparar com as regras e segurança definidas pela origem para procurar deriva de configuração.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Ver a resposta

A amostra a seguir é a resposta devolvida do comando anterior. Os resultados mostram todas as regras de segurança eficazes e aplicadas na máquina virtual avariadas em grupos de **Regras de Segurança de Rede, Regras** **de Segurança Padrão** e **Regras de Segurança Eficaz.**

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Visite [grupos de segurança de rede de auditoria (NSG) com o Observador de Rede](network-watcher-security-group-view-powershell.md) para aprender a automatizar a validação de Grupos de Segurança da Rede.