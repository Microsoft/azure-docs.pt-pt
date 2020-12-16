---
title: Converter um legado Exchange olhando para um recurso Azure usando PowerShell
titleSuffix: Azure
description: Converter um legado Exchange olhando para um recurso Azure usando PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: acc32f4916f5f7f8fe22eebdd1e72db297cac94c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590210"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Converter um legado Exchange olhando para um recurso Azure usando PowerShell

Este artigo descreve como converter um legado existente Exchange olhando para um recurso Azure usando cmdlets PowerShell.

Se preferir, pode completar este guia utilizando o [portal](howto-legacy-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Before you begin
* Reveja os [pré-requisitos](prerequisites.md) e o [Persimento do Persimento cambial](walkthrough-exchange-all.md) antes de iniciar a configuração.

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Converter um legado Exchange olhando para um recurso Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Obtenha o legado Exchange olhando para a conversão
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

### <a name="convert-legacy-peering"></a>Converter o olhar legado
Este comando pode ser usado para converter o legado Exchange espreitando para um recurso Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Quando converte o legado a espreitar para um recurso Azure, as modificações não são suportadas.
&nbsp;

Esta resposta de exemplo mostra quando o provisionamento de ponta a ponta foi concluído com sucesso:

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
Para obter mais informações, consulte [as PERGUNTAS Frequentes de Observação da Internet.](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um persco de troca utilizando o PowerShell](howto-exchange-powershell.md)
