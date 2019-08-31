---
title: Automatizar a auditoria do NSG com o modo de exibição do grupo de segurança do observador de rede do Azure | Microsoft Docs
description: Esta página fornece instruções sobre como configurar a auditoria de um grupo de segurança de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 8e0eddd07fc0c473e4777d9dd90d0b2c64145e34
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165144"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizar a auditoria de NSG com a exibição do grupo de segurança do observador de rede do Azure

Os clientes costumam enfrentar o desafio de verificar a postura de segurança de sua infraestrutura. Esse desafio não é diferente para suas VMs no Azure. É importante ter um perfil de segurança semelhante com base nas regras do NSG (grupo de segurança de rede) aplicadas. Usando o modo de exibição grupo de segurança, agora você pode obter a lista de regras aplicadas a uma VM em um NSG. Você pode definir um perfil de segurança Golden NSG e iniciar a exibição de grupo de segurança em uma cadência semanal e comparar a saída com o perfil de ouro e criar um relatório. Dessa forma, você pode identificar com facilidade todas as VMs que não estão em conformidade com o perfil de segurança prescrito.

Se você não estiver familiarizado com grupos de segurança de rede, consulte [visão geral de segurança de rede](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, você compara uma linha de base válida conhecida com os resultados da exibição do grupo de segurança retornados para uma máquina virtual.

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também pressupõe que um grupo de recursos com uma máquina virtual válida exista para ser usado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo Obtém a exibição de grupo de segurança para uma máquina virtual.

Nesse cenário, você irá:

- Recuperar um conjunto de regras bom conhecido
- Recuperar uma máquina virtual com a API REST
- Obter exibição de grupo de segurança para a máquina virtual
- Avaliar resposta

## <a name="retrieve-rule-set"></a>Recuperar conjunto de regras

A primeira etapa neste exemplo é trabalhar com uma linha de base existente. O exemplo a seguir é um JSON extraído de um grupo de segurança de `Get-AzNetworkSecurityGroup` rede existente usando o cmdlet que é usado como a linha de base para este exemplo.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Converter conjunto de regras em objetos do PowerShell

Nesta etapa, estamos lendo um arquivo JSON que foi criado anteriormente com as regras que devem estar no grupo de segurança de rede para este exemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Recuperar observador de rede

A próxima etapa é recuperar a instância do observador de rede. A `$networkWatcher` variável é passada para o `AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessária para executar o `Get-AzNetworkWatcherSecurityGroupView` cmdlet. O exemplo a seguir obtém um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Recuperar exibição de grupo de segurança

A próxima etapa é recuperar o resultado da exibição do grupo de segurança. Esse resultado é comparado ao JSON de "linha de base" que foi mostrado anteriormente.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisando os resultados

A resposta é agrupada por interfaces de rede. Os diferentes tipos de regras retornadas são regras de segurança efetivas e padrão. O resultado é dividido por como ele é aplicado, seja em uma sub-rede ou em uma NIC virtual.

O script do PowerShell a seguir compara os resultados da exibição do grupo de segurança com uma saída existente de um NSG. O exemplo a seguir é um exemplo simples de como os resultados podem ser comparados com `Compare-Object` o cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

O exemplo a seguir é o resultado. Você pode ver que duas das regras que estavam no primeiro conjunto de regras não estavam presentes na comparação.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Passos seguintes

Se as configurações tiverem sido alteradas, consulte [gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md) para rastrear o grupo de segurança de rede e as regras de segurança que estão em questão.













