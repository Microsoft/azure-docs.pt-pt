---
title: Analisar a segurança de rede com a vista de grupo de segurança de observador de rede do Azure - REST API | Documentos da Microsoft
description: Este artigo descreve como utilizar o PowerShell para analisar uma segurança de máquinas virtuais com a vista de grupo de segurança.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: a2f55a65d88b499384fc961c3d6a479bac804ba8
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051534"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analisar a segurança de Máquina Virtual com a vista de grupo de segurança com a REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

Vista de grupo de segurança devolve as regras de segurança de rede configurado e eficaz que são aplicadas a uma máquina virtual. Esta funcionalidade é útil para auditar e diagnosticar os grupos de segurança de rede e regras que estão configuradas numa VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos como obter as regras de segurança eficazes e aplicadas a uma máquina virtual com a REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, chama a API de Rest do observador de rede para obter a vista de grupo de segurança para uma máquina virtual. ARMclient é utilizada para chamar a API de REST com o PowerShell. ARMClient encontra-se no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém as regras de segurança aplicados e eficiente para uma máquina virtual especificada.

## <a name="log-in-with-armclient"></a>Inicie sessão com ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperar uma máquina virtual

Execute o seguinte script para retornar um machineThe virtual o código a seguir precisa variáveis:

- **subscriptionId** -o id de subscrição também pode ser obtido com o **Get-AzSubscription** cmdlet.
- **resourceGroupName** -o nome do grupo de recursos que contém as máquinas virtuais.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A informação que é necessário é o **id** abaixo do tipo `Microsoft.Compute/virtualMachines` em resposta, como visto no exemplo a seguir:

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

## <a name="get-security-group-view-for-virtual-machine"></a>Obter vista de grupo de segurança para a máquina virtual

O exemplo seguinte solicita a vista de grupo de segurança de uma máquina de virtual de destino. Os resultados deste exemplo podem ser utilizados para comparar para as regras e definido pela origem procurar descompassos de configuração de segurança.

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

O exemplo a seguir é a resposta devolvida pelo comando anterior. Os resultados mostram todas as regras de segurança eficazes e aplicado na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, e  **EffectiveSecurityRules**.

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

## <a name="next-steps"></a>Passos Seguintes

Visite [auditoria rede grupos de segurança (NSG) com o observador de rede](network-watcher-security-group-view-powershell.md) para aprender a automatizar a validação dos grupos de segurança de rede.


