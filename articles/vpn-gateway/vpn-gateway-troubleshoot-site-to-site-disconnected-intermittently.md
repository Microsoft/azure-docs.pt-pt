---
title: Resolver problemas do Azure VPN Site a Site desliga-se intermitentemente | Documentos da Microsoft
description: Saiba como resolver o problema em que a ligação VPN de Site a Site desligado regularmente.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 2fdd82c2f0c96b3bd20231911bb88cf54c172931
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457769"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Resolução de problemas: Azure VPN de Site a Site desliga-se intermitentemente

Podem ocorrer o problema que uma ligação de VPN de Site a Site do Microsoft Azure nova ou existente não está estável ou desliga regularmente. Este artigo fornece passos para ajudar a identificar e resolver a causa do problema de resolução de problemas. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="prerequisite-step"></a>Passo dos pré-requisitos

Verifique o tipo de gateway de rede virtual do Azure:

1. Aceda a [portal do Azure](https://portal.azure.com).
2. Verifique os **descrição geral** página do gateway de rede virtual para obter as informações de tipo.
    
    ![A descrição geral do gateway](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1 Verifique se o dispositivo VPN no local é validado

1. Verifique se estão a utilizar um [validado o dispositivo VPN e versão do sistema operativo](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo VPN não for validado, poderá ter de contactar o fabricante do dispositivo para ver se há qualquer problema de compatibilidade.
2. Certifique-se de que o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [editar os exemplos de configuração do dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Passo 2 Verifique as definições de associação de segurança (para gateways de rede virtual do Azure baseada em políticas)

1. Certifique-se de que a rede virtual, sub-redes e intervalos no **gateway de rede Local** definição no Microsoft Azure são as mesmas que a configuração no dispositivo VPN no local.
2. Certifique-se de que correspondem às definições de associação de segurança.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Passo 3 de verificação para rotas definidas pelo utilizador ou grupos de segurança de rede na sub-rede de Gateway

Uma rota definida pelo utilizador na sub-rede de gateway pode ser restringir algum tráfego e permitindo que o outro tráfego. Isso faz parecer que a ligação VPN é pouco fiáveis para algum tráfego e boa para os outros utilizadores. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Passo 4 verificar o "um túnel VPN por par sub-rede" (para gateways de rede virtual baseada em políticas)

Certifique-se de que o dispositivo VPN no local está definido como ter **um túnel VPN por par de sub-rede** para gateways de rede virtual baseada em políticas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Passo 5 de verificação para a limitação de associação de segurança (para gateways de rede virtual baseada em políticas)

O gateway de rede virtual baseada em política tem um limite de 200 pares de associação de segurança de sub-rede. Se o número de sub-redes de rede virtual do Azure multiplicado vezes pelo número de sub-redes locais é superior a 200, ver esporádicos sub-redes a desligar.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Passo 6 verificação no local o endereço de interface externa do dispositivo VPN

- Se o endereço IP do dispositivo VPN de acesso à Internet está incluída nos **gateway de rede Local** definição no Azure, poderá notar interrupções de ligação esporádicos.
- Interface externa do dispositivo tem de ser diretamente na Internet. Não deve haver tradução de endereços de rede (NAT) ou firewall entre a Internet e o dispositivo.
-  Se configurar o Clustering de Firewall para ter um IP virtual, tem de interromper o cluster e expor a aplicação de VPN diretamente a uma interface pública que o gateway pode interagir com o.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Passo 7 de verificação se o dispositivo VPN no local tem Perfect Forward Secrecy ativada

O **Perfect Forward Secrecy** funcionalidade pode causar os problemas de Desativação. Se o dispositivo VPN tiver **perfeitas Secrecy reencaminhar** ativada, desative a funcionalidade. Em seguida, [atualizar o gateway de rede virtual da diretiva IPsec](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Passos Seguintes

- [Configurar uma ligação Site a Site a uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Configurar a política de IPsec/IKE para ligações VPN de Site a Site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

