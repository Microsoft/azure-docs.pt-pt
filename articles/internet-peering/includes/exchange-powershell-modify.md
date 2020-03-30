---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774422"
---
A seguinte operação de modificação é suportada para o peering de troca
1. Adicionar ligações de persparo de troca
1. Remover ligações de persparo de troca
1. Adicione a sessão IPv4/IPv6 em ligações ativas.
1. Remova a sessão IPv4/IPv6 sobre ligações ativas.


### <a name="add-exchange-peering-connections"></a>Adicionar ligações de persparo de troca

Abaixo o exemplo descreve como adicionar ligações ao peering cambial existente

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Remover ligações de persparo de troca

Abaixo o exemplo descreve como remover ligações ao peering cambial existente

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Veja todas as ligações e selecione a ligação que pretende remover. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

No comando abaixo, em vez de 0, introduza o número de índice para a ligação que pretende remover.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicione a sessão IPv4/IPv6 sobre ligações ativas

Abaixo o exemplo descreve como adicionar a sessão IPv6 à conexão cambial existente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 sobre ligações ativas

A remoção de uma sessão IPv4/IPv6 de uma ligação existente não é suportada atualmente no PowerShell. Contacte [o peering da Microsoft](mailto:peeringexperience@microsoft.com).