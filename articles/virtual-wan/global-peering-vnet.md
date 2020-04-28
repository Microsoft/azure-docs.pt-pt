---
title: Configure Global VNet peering for Azure Virtual WAN [ Microsoft Docs
description: Ligue um VNet numa região diferente ao seu centro virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73588229"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configure Global VNet peering (VNet cross-region) para Virtual WAN

Pode ligar um VNet numa região diferente ao seu centro virtual WAN.

## <a name="before-you-begin"></a>Antes de começar

Verifique se preencheu os seguintes critérios:

* O VNet transversal (falado) não está ligado a outro centro virtual WAN. Um discurso só pode ser ligado a um centro virtual.
* O VNet (falado) não contém um portal de rede virtual (por exemplo, um Gateway De rede virtual Azure VPN ou ExpressRoute). Se o VNet contiver um portal de rede virtual, deve remover o portal antes de ligar o VNet falado ao centro.

## <a name="register-this-feature"></a><a name="register"></a>Registar esta funcionalidade

Pode registar-se nesta funcionalidade utilizando o PowerShell. Se selecionar "Try It" a partir do exemplo abaixo, a Azure Cloud-Shell abre e não terá de instalar os cmdlets PowerShell localmente no seu computador. Se necessário, pode alterar as subscrições utilizando o cmdlet 'Select-AzSubscription -SubscriptionId <subid>'.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Verificar o registo

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Ligue um VNet ao centro

Neste passo, cria-se a ligação de pares entre o seu hub e o VNet transversal. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a ligação de peering.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o WAN Virtual, consulte a [visão geral virtual wan](virtual-wan-about.md).