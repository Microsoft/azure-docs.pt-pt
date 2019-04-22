---
title: 'Como configurar OpenVPN no Gateway de VPN do Azure: PowerShell| Microsoft Docs'
description: Passos para configurar OpenVPN para o Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281969"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Configurar OpenVPN para o Gateway de VPN do Azure ponto a site (pré-visualização)

Este artigo ajuda-o a configurar **OpenVPN® protocolo** no Gateway de VPN do Azure. O artigo pressupõe que já tem um ambiente de ponto a site de trabalho. Se não o fizer, utilize as instruções no passo 1 para criar uma VPN ponto a site.

> [!IMPORTANT]
> Esta Pré-visualização Pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>Registar esta funcionalidade

Clique nas **TryIt** nestes passos para registar esta funcionalidade facilmente utilizando o Azure Cloud Shell.

>[!NOTE]
>Se não registar esta funcionalidade, não poderá utilizá-lo.
>

Depois de clicar em **TryIt** para abrir o Azure Cloud Shell, copie e cole os seguintes comandos:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Assim que a funcionalidade for apresentada como estando registada, volte a registar a subscrição no espaço de nomes do Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Criar uma VPN ponto a site

Se ainda não tiver um ambiente de ponto a site está a funcionar, siga as instruções para criar um. Ver [criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificados nativa do Azure. 

> [!IMPORTANT]
> O SKU básico não é suportado para OpenVPN.

## <a name="enable"></a>2. Ativar OpenVPN no gateway

Ative OpenVPN no seu gateway. Certifique-se de que o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os seguintes comandos:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passos Seguintes

Para configurar clientes para OpenVPN, consulte [OpenVPN configurar clientes](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca registada da OpenVPN Inc.**