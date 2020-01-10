---
title: Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o PowerShell
titleSuffix: Azure
description: Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775397"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o PowerShell

Este artigo descreve como converter um emparelhamento do Exchange herdado existente para recursos do Azure usando cmdlets do PowerShell.

Se preferir, você pode concluir este guia usando o [portal](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [emparelhamento do Exchange](walkthrough-exchange-all.md) com o Walkthrough antes de começar a configuração.

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Obter o emparelhamento do Exchange herdado para conversão
Veja abaixo o exemplo para obter o emparelhamento do Exchange herdado no local de emparelhamento de Seattle:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

A resposta é semelhante ao seguinte exemplo:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Converter emparelhamento herdado
O comando abaixo pode ser usado para converter o emparelhamento do Exchange herdado para o recurso do Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Observe que, ao converter o emparelhamento herdado no recurso do Azure, não há suporte para modificações &nbsp;

Abaixo está um exemplo de resposta quando o provisionamento de ponta a ponta foi concluído com êxito:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Recursos adicionais
Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```
Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md)
