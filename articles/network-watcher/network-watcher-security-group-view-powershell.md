---
title: Analisar segurança de rede - Security Group View - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como usar o PowerShell para analisar uma segurança de máquinas virtuais com a Vista do Grupo de Segurança.
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
ms.openlocfilehash: b87d076c12a40d84dc99ef5b48454afa3f0ffce0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738734"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analise a segurança da sua máquina virtual com a vista do grupo de segurança usando o PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

A visão do grupo de segurança devolve regras de segurança configuradas e eficazes de segurança de rede que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar grupos de segurança de rede e regras que são configuradas num VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, mostramos-lhe como recuperar as regras de segurança configuradas e eficazes para uma máquina virtual usando o PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Before you begin

Neste cenário, você corre o `Get-AzNetworkWatcherSecurityGroupView` cmdlet para recuperar a informação da regra de segurança.

Este cenário pressupõe que já seguiu os passos na [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Scenario

O cenário abordado neste artigo recupera as regras de segurança configuradas e eficazes para uma determinada máquina virtual.

## <a name="retrieve-network-watcher"></a>Observador de rede de recuperação

O primeiro passo é recuperar a instância do Observador de Rede. Esta variável é passada para o `Get-AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Obter um VM

É necessária uma máquina virtual para executar o `Get-AzNetworkWatcherSecurityGroupView` cmdlet contra. O exemplo a seguir obtém um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Recuperar vista do grupo de segurança

O próximo passo é recuperar o resultado da vista do grupo de segurança.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Visualização dos resultados

O exemplo a seguir é uma resposta mais reduzida dos resultados devolvidos. Os resultados mostram todas as regras de segurança eficazes e aplicadas na máquina virtual avariadas em grupos de **Regras de Segurança de Rede, Regras** **de Segurança Padrão**e **Regras de Segurança Eficaz.**

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Próximos passos

Visite [grupos de segurança de rede de auditoria (NSG) com o Observador de Rede](network-watcher-nsg-auditing-powershell.md) para aprender a automatizar a validação de Grupos de Segurança da Rede.


