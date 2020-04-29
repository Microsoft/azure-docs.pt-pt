---
title: Analisar a segurança da rede com a Visão de Grupo de Segurança - Azure CLI
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como usar o Azure CLI para analisar uma segurança de máquinas virtuais com a Security Group View.
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
ms.openlocfilehash: 73f1efc512bf031021791da8cc55bc4e7d98a812
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76840780"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analise a sua segurança da Máquina Virtual com vista de grupo de segurança usando o Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

A visão do grupo de segurança devolução de devoluções configuradas e eficazes de segurança da rede que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar Grupos de Segurança da Rede e regras que estão configuradas num VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos-lhe como recuperar as regras de segurança configuradas e eficazes a uma máquina virtual usando o Azure CLI

Para executar os passos deste artigo, é necessário [instalar a interface de linha de comando Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já seguiu os passos na [Create a Network Watcher](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança configuradas e eficazes para uma determinada máquina virtual.

## <a name="get-a-vm"></a>Obter um VM

É necessária uma máquina virtual `vm list` para executar o cmdlet. O seguinte comando lista as máquinas virtuais num grupo de recursos:

```azurecli
az vm list -resource-group resourceGroupName
```

Assim que conhecer a máquina virtual, pode utilizar o cmdlet para obter o `vm show` seu recurso Id:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Recuperar a visão do grupo de segurança

O próximo passo é recuperar o resultado da visão do grupo de segurança.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visualização dos resultados

O exemplo seguinte é uma resposta encurtada dos resultados devolvidos. Os resultados mostram todas as regras de segurança eficazes e aplicadas na máquina virtual desagregadas em grupos de **NetworkInterfaceSecurityRules,** **DefaultSecurityRules**e **EffectiveSecurityRules**.

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

Visite os Grupos de Segurança da [Rede auditada (NSG) com](network-watcher-nsg-auditing-powershell.md) o Observador da Rede para saber automatizar a validação de Grupos de Segurança da Rede.

Saiba mais sobre as regras de segurança que são aplicadas aos recursos da sua rede visitando a [visão geral](network-watcher-security-group-view-overview.md) do grupo de segurança
