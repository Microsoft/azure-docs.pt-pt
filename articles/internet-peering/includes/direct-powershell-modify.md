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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680800"
---
Esta secção descreve como realizar as seguintes operações de modificação para o peering direto:

* Adicione ligações de observação direta.
* Remova as ligações de observação direta.
* Atualize ou desafina a largura de banda em ligações ativas.
* Adicione sessões IPv4 ou IPv6 em ligações ativas.
* Remova as sessões IPv4 ou IPv6 em ligações ativas.

### <a name="add-direct-peering-connections"></a>Adicionar ligações de peering direto

Este exemplo descreve como adicionar ligações ao peering direto existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Remover ligações de peering direto

Remover uma ligação não é suportado atualmente na PowerShell. Para mais informações, contacte a [Microsoft a espreitar](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desvalorizar largura de banda em ligações ativas

Este exemplo descreve como adicionar 10 Gbps a uma ligação direta existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Adicione sessões IPv4 ou IPv6 em conexões Ativas

Este exemplo descreve como adicionar uma sessão IPv6 numa ligação direta existente com apenas uma sessão IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Remova as sessões IPv4 ou IPv6 em ligações ativas

A remoção de uma sessão IPv4 ou IPv6 de uma ligação existente não é suportada atualmente no PowerShell. Para mais informações, contacte a [Microsoft a espreitar](mailto:peeringexperience@microsoft.com).