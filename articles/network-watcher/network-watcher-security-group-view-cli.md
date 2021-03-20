---
title: Analisar segurança de rede com Vista do Grupo de Segurança - Azure CLI
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como usar o Azure CLI para analisar uma segurança de máquinas virtuais com a Vista do Grupo de Segurança.
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
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94948651"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analise a segurança da sua máquina virtual com vista do grupo de segurança usando o Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

> [!NOTE]
> A API do Grupo de Segurança já não está a ser mantida e será depreciada em breve. Utilize a [funcionalidade Regras de Segurança Eficazes](./network-watcher-security-group-view-overview.md) que fornece a mesma funcionalidade.


A visão do grupo de segurança devolve regras de segurança configuradas e eficazes de segurança de rede que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar grupos de segurança de rede e regras que são configuradas num VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos-lhe como recuperar as regras de segurança configuradas e eficazes para uma máquina virtual usando O Azure CLI

Para executar os passos neste artigo, é necessário [instalar a interface de linha de comando Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já seguiu os passos na [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Scenario

O cenário abordado neste artigo recupera as regras de segurança configuradas e eficazes para uma determinada máquina virtual.

## <a name="get-a-vm"></a>Obter um VM

É necessária uma máquina virtual para executar o `vm list` cmdlet. O seguinte comando lista as máquinas virtuais num grupo de recursos:

```azurecli
az vm list -resource-group resourceGroupName
```

Assim que conhecer a máquina virtual, pode utilizar o `vm show` cmdlet para obter o seu ID de recursos:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Recuperar vista do grupo de segurança

O próximo passo é recuperar o resultado da vista do grupo de segurança.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visualização dos resultados

O exemplo a seguir é uma resposta mais reduzida dos resultados devolvidos. Os resultados mostram todas as regras de segurança eficazes e aplicadas na máquina virtual avariadas em grupos de **Regras de Segurança de Rede, Regras** **de Segurança Padrão** e **Regras de Segurança Eficaz.**

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Visite [grupos de segurança de rede de auditoria (NSG) com o Observador de Rede](network-watcher-nsg-auditing-powershell.md) para aprender a automatizar a validação de Grupos de Segurança da Rede.

Saiba mais sobre as regras de segurança que são aplicadas aos seus recursos de rede visitando a [visão geral do grupo de Segurança](network-watcher-security-group-view-overview.md)