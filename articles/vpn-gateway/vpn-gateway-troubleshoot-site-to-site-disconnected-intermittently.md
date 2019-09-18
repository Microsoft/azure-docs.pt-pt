---
title: Solucionar problemas de desconexão de VPN site a site do Azure de forma intermitente | Microsoft Docs
description: Saiba como solucionar o problema no qual a conexão VPN site a site foi desconectada regularmente.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 394062257f09bb0b8cfa6875795b88d35404f87c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058843"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Solução A VPN site a site do Azure se desconecta intermitentemente

Você pode enfrentar o problema de uma conexão VPN site a site Microsoft Azure nova ou existente não é estável ou se desconecta regularmente. Este artigo fornece etapas de solução de problemas para ajudá-lo a identificar e resolver a causa do problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="prerequisite-step"></a>Etapa de pré-requisito

Verifique o tipo de gateway de rede virtual do Azure:

1. Vá para [portal do Azure](https://portal.azure.com).
2. Verifique a página **visão geral** do gateway de rede virtual para obter as informações de tipo.
    
    ![A visão geral do gateway](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Etapa 1 verificar se o dispositivo VPN local é validado

1. Verifique se você está usando um [dispositivo VPN validado e uma versão do sistema operacional](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo VPN não for validado, talvez seja necessário entrar em contato com o fabricante do dispositivo para ver se há algum problema de compatibilidade.
2. Verifique se o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [editando exemplos de configuração de dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Etapa 2 verificar as configurações de associação de segurança (para gateways de rede virtual do Azure baseados em políticas)

1. Verifique se a rede virtual, as sub-redes e os intervalos na definição de **Gateway de rede local** em Microsoft Azure são iguais à configuração no dispositivo VPN local.
2. Verifique se as configurações de associação de segurança correspondem.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Etapa 3 verificar as rotas definidas pelo usuário ou os grupos de segurança de rede na sub-rede de gateway

Uma rota definida pelo usuário na sub-rede do gateway pode estar restringindo algum tráfego e permitindo outro tráfego. Isso faz parecer que a conexão VPN não é confiável para algum tráfego e é boa para outras pessoas. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Etapa 4 verificar a configuração "um túnel VPN por par de sub-rede" (para gateways de rede virtual com base em políticas)

Verifique se o dispositivo VPN local está definido para ter **um túnel VPN por par de sub-rede** para gateways de rede virtual baseados em políticas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Etapa 5 verificar a limitação de associação de segurança (para gateways de rede virtual baseada em políticas)

O gateway de rede virtual baseado em política tem um limite de 200 pares de associação de segurança de sub-rede. Se o número de sub-redes de rede virtual do Azure multiplicado por tempo pelo número de sub-redes locais for maior que 200, você verá sub-redes esporádicas desconectadas.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Etapa 6 verificar o endereço da interface externa do dispositivo VPN local

- Se o endereço IP voltado para a Internet do dispositivo VPN estiver incluído na definição de **Gateway de rede local** no Azure, você poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver NAT (conversão de endereços de rede) ou firewall entre a Internet e o dispositivo.
-  Se você configurar o clustering de firewall para ter um IP virtual, deverá interromper o cluster e expor o dispositivo VPN diretamente a uma interface pública com a qual o gateway pode interagir.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Etapa 7 verificar se o dispositivo VPN local tem o sigilo de encaminhamento perfeito habilitado

O recurso de **sigilo de encaminhamento perfeito** pode causar problemas de desconexão. Se o dispositivo VPN tiver o **sigilo de encaminhamento perfeito** habilitado, desabilite o recurso. Em seguida, [atualize a política IPsec do gateway de rede virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Passos Seguintes

- [Configurar uma conexão site a site com uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Configurar a política de IPsec/IKE para conexões VPN site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

