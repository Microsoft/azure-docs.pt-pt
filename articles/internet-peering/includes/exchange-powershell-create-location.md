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
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587859"
---
O PowerShell cmdlet **Get-AzPeeringLocation** devolve uma lista de locais `Kind`de observação com o parâmetro obrigatório , que utilizará em etapas posteriores.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Os locais de intercâmbio de pares contêm os seguintes campos:
* Nome de câmbio
* Localização de peering
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Endereço MicrosoftIPv4
* Endereço MicrosoftIPv6

Valide que está presente na instalação de peering desejada, referindo-se ao [PeeringDB](https://www.peeringdb.com).

Este exemplo mostra como usar Seattle como o local de observação para criar um olhar.

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
