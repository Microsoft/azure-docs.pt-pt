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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774292"
---
O cmdlet **Get-AzPeeringLocation** do PowerShell retorna uma lista de locais de emparelhamento com o parâmetro obrigatório `Kind`, que você usará em etapas posteriores:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Os locais de emparelhamento direto contêm os seguintes campos:
* PeeringLocation 
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Valide se você está presente na instalação de emparelhamento desejada fazendo referência a [PeeringDB](https://w www.peeringdb.com).

Veja abaixo um exemplo que mostra como usar Seattle como o local de emparelhamento para criar um emparelhamento direto:

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```