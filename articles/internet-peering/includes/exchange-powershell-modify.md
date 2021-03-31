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
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678678"
---
As seguintes operações de modificação são suportadas para o persimento cambial:
* Adicione as ligações de persimento cambial.
* Remova as ligações de persimento do Exchange.
* Adicione uma sessão IPv4 ou IPv6 em ligações Ativas.
* Remova uma sessão IPv4 ou IPv6 em ligações Ativas.


### <a name="add-exchange-peering-connections"></a>Adicionar ligações de persituo de troca

Este exemplo descreve como adicionar ligações a um perscrutivo cambial existente.

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

### <a name="remove-exchange-peering-connections"></a>Remover ligações de persimento de troca

Este exemplo descreve como remover as ligações a um perscrutivo cambial existente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Ver todas as ligações e selecionar a ligação que pretende remover. 

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

No comando seguinte, em vez de 0, introduza o número de índice para a ligação que pretende remover.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Adicione uma sessão IPv4 ou IPv6 em ligações Ativas

Este exemplo descreve como adicionar uma sessão IPv6 a uma ligação de Troca existente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Remover uma sessão IPv4 ou IPv6 em ligações ativas

A remoção de uma sessão IPv4 ou IPv6 de uma ligação existente não é suportada atualmente no PowerShell. Para mais informações, contacte [a Microsoft a espreitar.](mailto:peeringexperience@microsoft.com)