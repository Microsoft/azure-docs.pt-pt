---
title: ficheiro de inclusão
titleSuffix: Azure
description: ficheiro de inclusão
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82587859"
---
O cmdlet **Get-AzPeeringLocation** powerShell devolve uma lista de locais de observação com o parâmetro obrigatório `Kind` , que utilizará em etapas posteriores.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Os locais de permuta contêm os seguintes campos:
* Nome de troca
* Localização de pares
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Valide que está presente na instalação de observação desejada, referindo-se ao [PeeringDB](https://www.peeringdb.com).

Este exemplo mostra como usar Seattle como o local de observação para criar um espreitamento.

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
