---
title: 'Criar e definir políticas IPsec personalizadas para ponto a local: PowerShell'
titleSuffix: Azure VPN Gateway
description: Este artigo ajuda-o a criar e definir políticas IPSec personalizadas para configurações P2S de Gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91743697"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>Criar e definir políticas IPsec personalizadas para ponto a local (pré-visualização)

Se o seu ambiente necessitar de uma política IPsec personalizada para encriptação, pode configurar facilmente um objeto de política com as definições necessárias. Este artigo ajuda-o a criar um objeto de política personalizado e, em seguida, defini-lo usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Verifique se o seu ambiente cumpre os seguintes requisitos:

* Tem uma VPN de ponto a local já configurada. Se não o fizer, configuure um utilizando os passos, crie um artigo **VPN ponto a local**  utilizando o [PowerShell,](vpn-gateway-howto-point-to-site-rm-ps.md)ou o [portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. Definir variáveis

Declare as variáveis que quer utilizar. Utilize a seguinte amostra, substituindo os valores para si próprio quando necessário. Se fechar a sessão PowerShell/Cloud Shell em qualquer ponto durante o exercício, basta copiar e colar novamente os valores para redeclarecer as variáveis.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. Criar objeto de política

Crie um objeto de política IPsec personalizado. Pode ajustar os valores para cumprir os critérios necessários.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. Atualizar gateway e definir política

Neste passo, atualize o seu gateway P2S VPN existente e desave a política do IPsec.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações P2S, consulte [Sobre a VPN ponto-a-local](point-to-site-about.md).
