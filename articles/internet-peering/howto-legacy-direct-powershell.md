---
title: Converter um emparelhamento direto herdado para o recurso do Azure usando o PowerShell
titleSuffix: Azure
description: Converter um emparelhamento direto herdado para o recurso do Azure usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775020"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Converter um emparelhamento direto herdado para o recurso do Azure usando o PowerShell

Este artigo descreve como converter um emparelhamento direto herdado existente para recursos do Azure usando cmdlets do PowerShell.

Se preferir, você pode concluir este guia usando o [portal](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine [pré-requisitos](prerequisites.md) e [direcionamento direto de emparelhamento](walkthrough-direct-all.md) antes de começar a configuração.

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Converter o emparelhamento direto herdado para o recurso do Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Obter emparelhamento direto herdado para conversão
Veja abaixo um exemplo para obter o emparelhamento direto herdado no local de emparelhamento de Seattle

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Veja abaixo um exemplo de resposta:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-legacy-direct-peering"></a>Converter emparelhamento direto herdado

&nbsp;
> [!IMPORTANT]
> Observe que, ao converter o emparelhamento herdado no recurso do Azure, não há suporte para modificações. &nbsp;

Use o comando abaixo para converter o emparelhamento direto herdado para o recurso do Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Veja abaixo um exemplo de resposta:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Recursos adicionais
Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Crie ou modifique um emparelhamento direto usando o PowerShell](howto-direct-powershell.md).
