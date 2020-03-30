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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774292"
---
PowerShell cmdlet **Get-AzPeeringLocation** devolve uma lista de locais `Kind`de observação com o parâmetro obrigatório , que utilizará em etapas posteriores:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Os locais de observação direta contêm os seguintes campos:
* Localização de peering 
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Largura de BandaOferece

Valide que está presente na instalação de peering desejada, referindo-se ao [PeeringDB](https://wwww.peeringdb.com).

Abaixo está um exemplo que mostra como usar Seattle como o local de observação para criar um olhar direto:

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