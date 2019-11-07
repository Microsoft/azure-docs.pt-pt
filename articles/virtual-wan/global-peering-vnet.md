---
title: Configurar o emparelhamento VNet global para a WAN virtual do Azure | Microsoft Docs
description: Conecte uma VNet em uma região diferente ao seu hub de WAN virtual.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588229"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configurar o emparelhamento VNet global (VNet entre regiões) para WAN virtual

Você pode conectar uma VNet em uma região diferente ao seu hub de WAN virtual.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atendeu aos seguintes critérios:

* A VNet entre regiões (spoke) não está conectada a outro hub WAN virtual. Um spoke só pode ser conectado a um único Hub virtual.
* A VNet (spoke) não contém um gateway de rede virtual (por exemplo, um gateway de VPN do Azure ou um gateway de rede virtual ExpressRoute). Se a VNet contiver um gateway de rede virtual, você deverá remover o gateway antes de conectar a VNet spoke ao Hub.

## <a name="register"></a>Registar esta funcionalidade

Você pode se registrar para esse recurso usando o PowerShell. Se você selecionar "experimentar" no exemplo abaixo, o Shell de nuvem do Azure será aberto e você não precisará instalar os cmdlets do PowerShell localmente em seu computador. Se necessário, você pode alterar as assinaturas usando o cmdlet ' Select-AzSubscription-SubscriptionId <subid>'.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify"></a>Verificar registro

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="hub"></a>Conectar uma VNet ao Hub

Nesta etapa, você cria a conexão de emparelhamento entre o Hub e a VNet entre regiões. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a ligação de peering.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN virtual, consulte [visão geral da WAN virtual](virtual-wan-about.md).