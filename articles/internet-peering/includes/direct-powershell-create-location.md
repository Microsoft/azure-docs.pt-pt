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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680804"
---
O PowerShell cmdlet **Get-AzPeeringLocation** devolve uma lista de locais `Kind`de observação com o parâmetro obrigatório , que utilizará em etapas posteriores.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Os locais de observação direta contêm os seguintes campos:
* Localização de peering 
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Largura de BandaOferece

Valide que está presente na instalação de observação desejada, referindo-se ao [PeeringDB](https://wwww.peeringdb.com).

Este exemplo mostra como usar Seattle como o local de observação para criar um olhar direto.

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