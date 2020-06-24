---
title: Configure VNet Global espreitando para Azure Virtual WAN / Microsoft Docs
description: Ligue um VNet numa região diferente ao seu hub VIRTUAL WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 78c06ec1d93dcda5d171099943c287a9e4f43bc1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750571"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configure o peering global VNet (VNet cross-region) para O WAN Virtual

Pode ligar um VNet numa região diferente ao seu hub Virtual WAN.

## <a name="before-you-begin"></a>Antes de começar

Verifique se cumpriu os seguintes critérios:

* O VNet cross-region (spoke) não está ligado a outro hub Virtual WAN. Um porta-voz só pode ser ligado a um centro virtual.
* O VNet (spoke) não contém um gateway de rede virtual (por exemplo, um Gateway Azure VPN ou um gateway de rede virtual ExpressRoute). Se o VNet contiver um gateway de rede virtual, deve remover o gateway antes de ligar o VNet falado ao hub.

## <a name="register-this-feature"></a><a name="register"></a>Registar esta funcionalidade

Pode registar-se para esta função utilizando o PowerShell. Se selecionar "Try It" a partir do exemplo abaixo, o Azure Cloud-Shell abre-se e não precisará de instalar os cmdlets PowerShell localmente para o computador. Se necessário, pode alterar subscrições utilizando o cmdlet 'Select-AzSubscription <subid> -SubscriptionId'.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Verificar registo

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Ligue um VNet ao hub

Neste passo, você cria a ligação de espreitar entre o seu hub e o VNet cross-region. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a ligação de peering.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o VIRTUAL WAN, consulte [a Visão Geral virtual wan.](virtual-wan-about.md)