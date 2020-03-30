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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774396"
---
PowerShell cmdlet **Get-AzPeeringLocation** devolve uma lista de locais `Kind`de observação com o parâmetro obrigatório , que utilizará em etapas posteriores:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Locais de troca de pares contêm os seguintes campos:
* Nome de câmbio
* Localização de peering
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Endereço MicrosoftIPv4
* Endereço MicrosoftIPv6

Valide que está presente na instalação de peering desejada, referindo-se ao [PeeringDB](https://wwww.peeringdb.com).

Abaixo está um exemplo que mostra como usar Seattle como o local de observação para criar um olhar:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```