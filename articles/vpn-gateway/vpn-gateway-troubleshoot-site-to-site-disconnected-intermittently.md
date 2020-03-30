---
title: Troubleshoot Azure Site-to-Site VPN desliga-se intermitentemente
description: Aprenda a resolver o problema em que a ligação VPN site-to-site se desligou regularmente.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862565"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Resolução de problemas: Azure Site-to-Site VPN desliga-se intermitentemente

Pode experimentar o problema de que uma nova ou existente ligação VPN site-to-site da Microsoft Azure não é estável ou se desliga regularmente. Este artigo fornece passos de resolução de problemas para ajudá-lo a identificar e resolver a causa do problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="prerequisite-step"></a>Passo pré-requisito

Verifique o tipo de gateway da rede virtual Azure:

1. Vá ao [portal Azure.](https://portal.azure.com)
2. Consulte a página **de visão geral** do portal de rede virtual para obter informações sobre o tipo.
    
    ![A visão geral do portal](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1 Verifique se o dispositivo VPN no local é validado

1. Verifique se está a utilizar um [dispositivo VPN validado e versão do sistema operativo](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo VPN não for validado, poderá ter de contactar o fabricante do dispositivo para ver se existe algum problema de compatibilidade.
2. Certifique-se de que o dispositivo VPN está corretamente configurado. Para mais informações, consulte as amostras de [configuração do dispositivo de edição](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Passo 2 Verifique as definições da Associação de Segurança (para gateways de rede virtual Azure baseados em políticas)

1. Certifique-se de que a rede virtual, as subredes e as gamas na definição de gateway de **rede local** no Microsoft Azure são iguais à configuração no dispositivo VPN no local.
2. Verifique se as definições da Associação de Segurança correspondem.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Passo 3 Verifique as rotas definidas pelo utilizador ou grupos de segurança da rede na subnet gateway

Uma rota definida pelo utilizador na subnet gateway pode estar a restringir algum tráfego e a permitir outro tráfego. Isto faz com que pareça que a ligação VPN não é fiável para algum tráfego e boa para outros. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Passo 4 Verifique a definição "um túnel VPN por Subnet Pair" (para gateways de rede virtual baseados em políticas)

Certifique-se de que o dispositivo VPN no local está definido para ter **um túnel VPN por par de sub-redes** para gateways de rede virtual baseados em políticas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Passo 5 Verificar a limitação da associação de segurança (para gateways de rede virtual baseados em políticas)

O gateway da rede virtual baseado em políticas tem limite de 200 pares de subnet Security Association. Se o número de subredes de rede virtual Azure multiplicadas vezes pelo número de subredes locais for superior a 200, vê-se subredes esporádicas desligadas.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Passo 6 Verifique o endereço de interface externa do dispositivo VPN no local

- Se o endereço IP virado para a Internet do dispositivo VPN estiver incluído na definição de gateway da **rede local** em Azure, poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver tradução de endereços de rede (NAT) ou firewall entre a Internet e o dispositivo.
-  Se configurar o Clustering firewall para ter um IP virtual, tem de quebrar o cluster e expor o aparelho VPN diretamente a uma interface pública com a quais o gateway possa interagir.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Passo 7 Verifique se o dispositivo VPN no local tem o Perfeito Segredo Avançado ativado

A função **de Sigilo Avançado Perfeito** pode causar os problemas de desconexão. Se o dispositivo VPN tiver **o Segredo perfeito** para a frente ativado, desative a funcionalidade. Em seguida, [atualize a política de gateway iPsec](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)da rede virtual .

## <a name="next-steps"></a>Passos seguintes

- [Configure uma ligação Site-a-Site a uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Configure a política IPsec/IKE para ligações VPN site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

