---
title: Automatizar o NSG de auditoria com a vista de grupo de segurança de observador de rede do Azure | Documentos da Microsoft
description: Esta página fornece instruções sobre como configurar a auditoria de um grupo de segurança de rede
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3d35860452aabb6aecc4e8549c7b5ce4447d7aa4
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047675"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizar o NSG de auditoria com a vista de grupo de segurança de observador de rede do Azure

Os clientes frequentemente enfrentam o desafio de verificação a postura de segurança da sua infra-estrutura de. Esse desafio não é diferente para as suas VMs no Azure. É importante ter um perfil de segurança semelhante, com base nas regras de grupo de segurança de rede (NSG) aplicadas. Utilizar a vista de grupo de segurança, agora pode obter a lista de regras aplicadas a uma VM dentro de um NSG. Pode definir um perfil de segurança NSG dourado e iniciar a vista de grupo de segurança a uma cadência semanal e comparar o resultado para o perfil de ouro e criar um relatório. Desta forma, pode identificar com facilidade todas as VMs que não estão em conformidade com o perfil de segurança previstas.

Se não estiver familiarizado com os grupos de segurança de rede, consulte [descrição geral da segurança de rede](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, é comparar uma linha de base de bom conhecida para os resultados de vista do grupo de segurança devolvidos para uma máquina virtual.

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a vista de grupo de segurança para uma máquina virtual.

Neste cenário, irá:

- Obter um conjunto de regras de bom conhecido
- Recuperar uma máquina virtual com a Rest API
- Obter vista de grupo de segurança para a máquina virtual
- Avaliar a resposta

## <a name="retrieve-rule-set"></a>Obter o conjunto de regras

Neste exemplo, a primeira etapa é trabalhar com uma linha de base existente. O exemplo seguinte é alguns json extraída do grupo de segurança de rede uma existente, utilizando o `Get-AzNetworkSecurityGroup` cmdlet que é utilizado como a linha de base para este exemplo.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Converter o conjunto de regras em objetos do PowerShell

Neste passo, vamos está a ler um ficheiro json que foi criado anteriormente com as regras que devem estar no grupo de segurança de rede para este exemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

A próxima etapa é obter a instância do observador de rede. O `$networkWatcher` variável é transmitida para o `AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obtenha uma VM

Uma máquina virtual é necessária para executar o `Get-AzNetworkWatcherSecurityGroupView` cmdlet contra. O exemplo seguinte obtém um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Obter vista de grupo de segurança

A próxima etapa é obter o resultado de vista do grupo de segurança. Este resultado é comparado com o json de "linha de base" que foi mostrado anteriormente.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisar os resultados

A resposta é agrupada por interfaces de rede. Os diferentes tipos de regras devolvidos entram em vigor e regras de segurança predefinidas. O resultado é ainda mais dividido por como é aplicada, numa sub-rede ou um NIC virtual.

O seguinte script do PowerShell compara os resultados da vista de grupo de segurança para uma saída existente de um NSG. O exemplo seguinte é um exemplo simples de como os resultados podem ser comparados ao `Compare-Object` cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

O exemplo seguinte é o resultado. Pode ver duas das regras que foram a primeira regra definidas não estavam presentes na comparação.

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

## <a name="next-steps"></a>Passos Seguintes

Se as definições foram alteradas, consulte [gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md) rastrear as regras de segurança e de grupo de segurança de rede que estão em questão.













