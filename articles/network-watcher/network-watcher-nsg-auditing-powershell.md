---
title: Automatizar a auditoria NSG - Vista do grupo de segurança
titleSuffix: Azure Network Watcher
description: Esta página fornece instruções sobre como configurar a auditoria de um Grupo de Segurança de Rede
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
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948753"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizar a auditoria da NSG com a visão do grupo de segurança do observador da rede Azure

Os clientes são muitas vezes confrontados com o desafio de verificar a postura de segurança da sua infraestrutura. Este desafio não é diferente para os seus VMs em Azure. É importante ter um perfil de segurança semelhante com base nas regras do Grupo de Segurança da Rede (NSG) aplicadas. Utilizando a Vista do Grupo de Segurança, pode agora obter a lista de regras aplicadas a um VM dentro de um NSG. Pode definir um perfil de segurança NSG dourado e iniciar a Vista do Grupo de Segurança numa cadência semanal e comparar a saída com o perfil dourado e criar um relatório. Desta forma pode identificar com facilidade todos os VMs que não estão em conformidade com o perfil de segurança prescrito.

Se não estiver familiarizado com os Grupos de Segurança da Rede, consulte [a Visão Geral de Segurança da Rede](../virtual-network/network-security-groups-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, compara-se uma boa linha de base conhecida com os resultados de visualização do grupo de segurança devolvidos para uma máquina virtual.

Este cenário pressupõe que já seguiu os passos na [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede. O cenário pressupõe ainda que exista um Grupo de Recursos com uma máquina virtual válida.

## <a name="scenario"></a>Scenario

O cenário abordado neste artigo obtém a visão do grupo de segurança para uma máquina virtual.

Neste cenário, irá:

- Recupere um conjunto de regras de boa regra conhecido
- Recupere uma máquina virtual com API de repouso
- Obtenha vista de grupo de segurança para máquina virtual
- Avaliar Resposta

## <a name="retrieve-rule-set"></a>Conjunto de regras de recuperação

O primeiro passo neste exemplo é trabalhar com uma linha de base existente. O exemplo a seguir é algum json extraído de um grupo de segurança de rede existente usando o `Get-AzNetworkSecurityGroup` cmdlet que é usado como base para este exemplo.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Converter conjunto de regras para objetos PowerShell

Neste passo, estamos a ler um ficheiro json que foi criado anteriormente com as regras que se espera que estejam no Grupo de Segurança da Rede para este exemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Observador de rede de recuperação

O próximo passo é recuperar a instância do Observador de Rede. A `$networkWatcher` variável é passada para o `AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Obter um VM

É necessária uma máquina virtual para executar o `Get-AzNetworkWatcherSecurityGroupView` cmdlet contra. O exemplo a seguir obtém um objeto VM.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Recuperar vista do grupo de segurança

O próximo passo é recuperar o resultado da vista do grupo de segurança. Este resultado é comparado com o json "baseline" que foi mostrado anteriormente.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisar os resultados

A resposta é agrupada por interfaces de rede. Os diferentes tipos de regras devolvidas são regras de segurança eficazes e predefinidos. O resultado é ainda dividido pela forma como é aplicado, quer numa sub-rede, quer num NIC virtual.

O seguinte script PowerShell compara os resultados da Vista do Grupo de Segurança com uma saída existente de um NSG. O exemplo a seguir é um exemplo simples de como os resultados podem ser comparados com `Compare-Object` o cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

O exemplo a seguir é o resultado. Pode ver-se que duas das regras que estavam na primeira regra definida não estavam presentes na comparação.

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

Se as definições tiverem sido alteradas, consulte [Os Grupos de Segurança da Rede](../virtual-network/manage-network-security-group.md) para localizar o grupo de segurança da rede e as regras de segurança que estão em causa.