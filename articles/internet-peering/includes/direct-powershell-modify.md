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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774266"
---
Esta seção descreve como executar as seguintes operações de modificação para o emparelhamento direto:

* Adicionar conexões de emparelhamento direto
* Remover conexões de emparelhamento direto
* Atualizar ou fazer downgrade da largura de banda em conexões ativas.
* Adicionar sessão IPv4/IPv6 em conexões ativas.
* Remova a sessão IPv4/IPv6 em conexões ativas.

### <a name="add-direct-peering-connections"></a>Adicionar conexões de emparelhamento direto

O exemplo abaixo descreve como adicionar conexões ao emparelhamento direto existente

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

### <a name="remove-direct-peering-connections"></a>Remover conexões de emparelhamento direto

Atualmente, não há suporte para a remoção de uma conexão no PowerShell. Entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou fazer downgrade da largura de banda em conexões ativas

O exemplo abaixo descreve como adicionar o 10 Gbps à conexão direta existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicionar sessão IPv4/IPv6 em conexões ativas.

O exemplo abaixo descreve como adicionar uma sessão IPv6 em uma conexão direta existente somente com a sessão IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 em conexões ativas.

Atualmente, não há suporte para a remoção de uma sessão IPv4/IPv6 de uma conexão existente no PowerShell. Entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).