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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680800"
---
Esta secção descreve como executar as seguintes operações de modificação para o espreguio direto:

* Adicione ligações diretas de espreitar.
* Remova as ligações diretas de espreitar.
* Atualize ou desclasse a largura de banda nas ligações Ative.
* Adicione sessões IPv4 ou IPv6 em ligações Ative.
* Remova as sessões IPv4 ou IPv6 em ligações Ativas.

### <a name="add-direct-peering-connections"></a>Adicionar ligações de observação direta

Este exemplo descreve como adicionar ligações ao persimento direto existente.

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

### <a name="remove-direct-peering-connections"></a>Remover ligações de observação direta

A remoção de uma ligação não é suportada atualmente no PowerShell. Para mais informações, contacte [a Microsoft a espreitar.](mailto:peeringexperience@microsoft.com)

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desclassificar largura de banda em ligações Ativas

Este exemplo descreve como adicionar 10 Gbps a uma ligação Direta existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Adicionar sessões IPv4 ou IPv6 em ligações ativas

Este exemplo descreve como adicionar uma sessão de IPv6 sobre uma ligação Direta existente com apenas uma sessão IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Remover sessões IPv4 ou IPv6 em ligações ativas

A remoção de uma sessão IPv4 ou IPv6 de uma ligação existente não é suportada atualmente no PowerShell. Para mais informações, contacte [a Microsoft a espreitar.](mailto:peeringexperience@microsoft.com)