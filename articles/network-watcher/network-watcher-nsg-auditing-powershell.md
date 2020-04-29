---
title: Automate a auditoria da NSG - Visão de grupo de segurança
titleSuffix: Azure Network Watcher
description: Esta página fornece instruções sobre como configurar a auditoria de um Grupo de Segurança da Rede
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
ms.openlocfilehash: 59c1b6e6c281a736a79d110bd7d943344bcd5130
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76840983"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizar auditoria da NSG com visão do grupo De segurança do Observador da Rede Azure

Os clientes são muitas vezes confrontados com o desafio de verificar a postura de segurança da sua infraestrutura. Este desafio não é diferente para os seus VMs em Azure. É importante ter um perfil de segurança semelhante baseado nas regras do Grupo de Segurança da Rede (NSG) aplicadas. Utilizando a Visão de Grupo de Segurança, pode agora obter a lista de regras aplicadas a um VM dentro de um NSG. Pode definir um perfil de segurança NSG dourado e iniciar a Visão de Grupo de Segurança numa cadência semanal e comparar a saída com o perfil dourado e criar um relatório. Desta forma, pode identificar com facilidade todos os VMs que não estejam em conformidade com o perfil de segurança prescrito.

Se não estiver familiarizado com os Grupos de Segurança da Rede, consulte a [visão geral da Segurança da Rede](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, compara-se uma boa linha de base conhecida com os resultados de visualização do grupo de segurança devolvidos para uma máquina virtual.

Este cenário pressupõe que já seguiu os passos na [Create a Network Watcher](network-watcher-create.md) para criar um Observador de Rede. O cenário também pressupõe que existe um Grupo de Recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a visão do grupo de segurança para uma máquina virtual.

Neste cenário, irá:

- Recuperar um conjunto de regras de boa regra conhecido
- Recupere uma máquina virtual com API de descanso
- Obtenha vista de grupo de segurança para máquina virtual
- Avaliar resposta

## <a name="retrieve-rule-set"></a>Recuperar conjunto de regras

O primeiro passo neste exemplo é trabalhar com uma linha de base existente. O exemplo seguinte é um json extraído de um `Get-AzNetworkSecurityGroup` grupo de segurança de rede existente usando o cmdlet que é usado como base para este exemplo.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Converter regra definida para objetos PowerShell

Neste passo, estamos a ler um ficheiro JSON que foi criado anteriormente com as regras que se espera que estejam no Grupo de Segurança da Rede para este exemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Observador de rede de recuperação

O próximo passo é recuperar a instância do Observador da Rede. A `$networkWatcher` variável é `AzNetworkWatcherSecurityGroupView` passada para o cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Obter um VM

É necessária uma máquina virtual `Get-AzNetworkWatcherSecurityGroupView` para passar o cmdlet contra. O exemplo que se segue recebe um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Recuperar a visão do grupo de segurança

O próximo passo é recuperar o resultado da visão do grupo de segurança. Este resultado é comparado com o json "baseline" que foi mostrado anteriormente.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisar os resultados

A resposta é agrunada por interfaces de rede. Os diferentes tipos de regras devolvidas são regras de segurança eficazes e predefinidas. O resultado é ainda desfeito pela forma como é aplicado, seja numa subneta ou num NIC virtual.

O seguinte script PowerShell compara os resultados da Visão de Grupo de Segurança a uma saída existente de um NSG. O exemplo que se segue é um simples `Compare-Object` exemplo de como os resultados podem ser comparados com cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

O exemplo que se segue é o resultado. Pode ver-se que duas das regras que estavam no primeiro conjunto de regras não estavam presentes na comparação.

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

Se as definições tiverem sido alteradas, consulte [a Manage Network Security Groups](../virtual-network/manage-network-security-group.md) para localizar o grupo de segurança da rede e as regras de segurança que estão em causa.













