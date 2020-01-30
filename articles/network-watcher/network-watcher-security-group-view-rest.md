---
title: Analisar a segurança da rede - Security Group View - Azure REST API
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como usar o PowerShell para analisar uma segurança de máquinas virtuais com a Security Group View.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c9c76e9c06d4c45a096cff79dac82bb80ebe25d1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840745"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analise a sua segurança da Máquina Virtual com vista de grupo de segurança usando a API REST

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

A visão do grupo de segurança devolução de devoluções configuradas e eficazes de segurança da rede que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar Grupos de Segurança da Rede e regras que estão configuradas num VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos-lhe como recuperar as regras de segurança eficazes e aplicadas a uma máquina virtual usando a API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, você liga para a API de descanso de vigilante de rede para obter a visão do grupo de segurança para uma máquina virtual. ARMclient é usado para chamar a API REST usando o PowerShell. ARMClient é encontrado no Chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também pressupõe que existe um Grupo de Recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança eficazes e aplicadas para uma determinada máquina virtual.

## <a name="log-in-with-armclient"></a>Fazer logon com ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperar uma máquina virtual

Executar o seguinte script para devolver uma máquina virtual O seguinte código precisa de variáveis:

- **subscriçãoId** - O id de subscrição também pode ser recuperado com o cmdlet **Get-AzSubscription.**
- **recursosGroupName** - O nome de um grupo de recursos que contém máquinas virtuais.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A informação necessária é o **id** sob o tipo `Microsoft.Compute/virtualMachines` em resposta, como se pode ver no seguinte exemplo:

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

O exemplo que se segue solicita a visão do grupo de segurança de uma máquina virtual direcionada. Os resultados deste exemplo podem ser usados para comparar com as regras e segurança definidas pela origem para procurar a deriva de configuração.

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

A amostra seguinte é a resposta devolvida do comando anterior. Os resultados mostram todas as regras de segurança eficazes e aplicadas na máquina virtual desagregadas em grupos de **NetworkInterfaceSecurityRules,** **DefaultSecurityRules**e **EffectiveSecurityRules**.

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

Visite os Grupos de Segurança da [Rede auditada (NSG) com](network-watcher-security-group-view-powershell.md) o Observador da Rede para saber automatizar a validação de Grupos de Segurança da Rede.


