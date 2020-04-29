---
title: Converter um legado Exchange peering para um recurso Azure usando powerShell
titleSuffix: Azure
description: Converter um legado Exchange peering para um recurso Azure usando powerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686780"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Converter um legado Exchange peering para um recurso Azure usando powerShell

Este artigo descreve como converter um legado existente Exchange peering para um recurso Azure usando cmdlets PowerShell.

Se preferir, pode completar este guia utilizando o [portal](howto-legacy-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o ["Exchange peering walkthrough"](walkthrough-exchange-all.md) antes de iniciar a configuração.

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Converter um legado Exchange peering para um recurso Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Obtenha o legado Exchange peering para conversão
Este exemplo mostra como obter o legado Exchange olhando para o local de observação de Seattle:

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

### <a name="convert-legacy-peering"></a>Converter o legado peering
Este comando pode ser usado para converter o legado Exchange peering para um recurso Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Quando se converte o legado a olhar para um recurso Azure, as modificações não são suportadas.
&nbsp;

Esta resposta de exemplo mostra quando o fornecimento de ponta a ponta foi concluído com sucesso:

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
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```
Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um persparo de troca utilizando o PowerShell](howto-exchange-powershell.md)
