---
title: Analisar a segurança de rede com a exibição do grupo de segurança do observador de rede do Azure-PowerShell | Microsoft Docs
description: Este artigo descreverá como usar o PowerShell para analisar uma segurança de máquinas virtuais com o modo de exibição de grupo de segurança.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4c7b79460169612a046b19a4d66f222936710a8e
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163905"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analise sua segurança de máquina virtual com a exibição de grupo de segurança usando o PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

A exibição de grupo de segurança retorna regras de segurança de rede configuradas e efetivas que são aplicadas a uma máquina virtual. Esse recurso é útil para auditar e diagnosticar grupos de segurança de rede e regras configurados em uma VM para garantir que o tráfego seja corretamente permitido ou negado. Neste artigo, mostraremos como recuperar as regras de segurança configuradas e efetivas para uma máquina virtual usando o PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, você executa o `Get-AzNetworkWatcherSecurityGroupView` cmdlet para recuperar as informações da regra de segurança.

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança configuradas e efetivas para uma determinada máquina virtual.

## <a name="retrieve-network-watcher"></a>Recuperar observador de rede

A primeira etapa é recuperar a instância do observador de rede. Essa variável é passada para o `Get-AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessária para executar o `Get-AzNetworkWatcherSecurityGroupView` cmdlet. O exemplo a seguir obtém um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Recuperar exibição de grupo de segurança

A próxima etapa é recuperar o resultado da exibição do grupo de segurança.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Exibindo os resultados

O exemplo a seguir é uma resposta abreviada dos resultados retornados. Os resultados mostram todas as regras de segurança efetivas e aplicadas na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**e **EffectiveSecurityRules**.

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

## <a name="next-steps"></a>Passos Seguintes

Visite [auditoria de grupos de segurança de rede (NSG) com](network-watcher-nsg-auditing-powershell.md) o observador de rede para saber como automatizar a validação de grupos de segurança de rede.


