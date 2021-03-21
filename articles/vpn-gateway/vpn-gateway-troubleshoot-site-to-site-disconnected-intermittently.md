---
title: Resolução de problemas Azure Site-to-Site VPN desliga intermitentemente
description: Saiba como resolver o problema em que a ligação VPN site-to-site foi desligada regularmente.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: genli
ms.openlocfilehash: 582077c46f5fc7176b457cf0d392af48fbe7d40b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369344"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Resolução de problemas: A VPN Site a Site do Azure desliga-se de forma intermitente

Pode sentir o problema de que uma ligação VPN VPN site-to-site do Microsoft Azure não seja estável ou desconecta regularmente. Este artigo fornece medidas de resolução de problemas para ajudá-lo a identificar e resolver a causa do problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="prerequisite-step"></a>Passo pré-requisito

Verifique o tipo de gateway de rede virtual Azure:

1. Ir para o [portal Azure.](https://portal.azure.com)
2. Consulte a página **geral** do gateway de rede virtual para obter informações sobre o tipo.
    
    ![A visão geral do portal](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1 Verifique se o dispositivo VPN no local é validado

1. Verifique se está a utilizar um [dispositivo VPN validado e uma versão do sistema operativo.](vpn-gateway-about-vpn-devices.md#devicetable) Se o dispositivo VPN não for validado, poderá ter de contactar o fabricante do dispositivo para ver se existe algum problema de compatibilidade.
2. Certifique-se de que o dispositivo VPN está corretamente configurado. Para obter mais informações, consulte [as amostras de configuração do dispositivo de edição](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Passo 2 Verifique as definições da Associação de Segurança (para gateways de rede virtual Azure baseados em políticas)

1. Certifique-se de que a rede virtual, sub-redes e.variações na definição **de gateway de rede local** no Microsoft Azure são as mesmas que a configuração no dispositivo VPN no local.
2. Verifique se as definições da Associação de Segurança coincidem.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Passo 3 Verifique se User-Defined rotas ou grupos de segurança da rede na sub-rede Gateway

Uma rota definida pelo utilizador na sub-rede gateway pode estar a restringir algum tráfego e a permitir outro tráfego. Isto faz parecer que a ligação VPN não é fiável para algum tráfego e é boa para outros. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Passo 4 Verifique a definição "um túnel VPN por par de sub-redes" (para portais de rede virtuais baseados em políticas)

Certifique-se de que o dispositivo VPN no local está definido para ter **um túnel VPN por par de sub-redes** para gateways de rede virtuais baseados em políticas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Passo 5 Verifique a limitação da associação de segurança (para gateways de rede virtuais baseados em políticas)

O gateway de rede virtual baseado em políticas tem um limite de 200 pares da Associação de Segurança sub-rede. Se o número de sub-redes de rede virtuais Azure multiplicado por tempos pelo número de sub-redes locais for superior a 200, vê-se a desconexão de sub-redes esporádicas.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Passo 6 Verifique no local endereço de interface externa do dispositivo VPN

- Se o endereço IP virado para a Internet do dispositivo VPN estiver incluído na definição **de gateway de rede local** em Azure, poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver tradução de endereço de rede (NAT) ou firewall entre a Internet e o dispositivo.
-  Se configurar o Cluster de Firewall para ter um IP virtual, tem de quebrar o cluster e expor o aparelho VPN diretamente a uma interface pública com a que o gateway pode interagir.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Passo 7 Verifique se o dispositivo VPN no local tem o sigilo perfeito para a frente ativado

A função **de sigilo perfeito para a frente** pode causar os problemas de desconexão. Se o dispositivo VPN tiver **o Sigilo perfeito para a frente** ativado, desative a funcionalidade. Em seguida, [atualize a política virtual de gateway IPsec](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Passos seguintes

- [Configure uma ligação site-a-local a uma rede virtual](./tutorial-site-to-site-portal.md)
- [Configure a política IPsec/IKE para ligações VPN site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
