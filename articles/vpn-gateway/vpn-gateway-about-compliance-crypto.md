---
title: 'Gateway Azure VPN: Requisitos criptográficos'
description: Este artigo discute os requisitos criptográficos e os gateways Azure VPN
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902830"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre requisitos criptográficos e gateways Azure VPN

Este artigo discute como pode configurar gateways Azure VPN para satisfazer os seus requisitos criptográficos para ambos os túneis S2S VPN e ligações VNet-to-VNet dentro do Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Sobre IKEv1 e IKEv2 para ligações Azure VPN

Tradicionalmente, permitimos ligações IKEv1 apenas para UsC Básico e permitimos ligações IKEv2 para todas as SKUs de gateway VPN que não as SKUs Básicas. As SKUs básicas permitem apenas 1 ligação e, juntamente com outras limitações, tais como o desempenho, os clientes que usam dispositivos legados que suportam apenas protocolos IKEv1 estavam tendo experiência limitada. De forma a melhorar a experiência dos clientes que utilizam protocolos IKEv1, estamos agora a permitir ligações IKEv1 para todas as SKUs de gateway VPN, exceto SKU Básico. Para mais informações, consulte [VPN Gateway SKUs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Ligações Azure VPN Gateway IKEv1 e IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Quando as ligações IKEv1 e IKEv2 são aplicadas à mesma porta de entrada VPN, o trânsito entre estas duas ligações é ativado automaticamente.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros políticos IPsec e IKE para gateways Azure VPN

O protocolo IPsec e o ike standard suportam uma ampla gama de algoritmos criptográficos em várias combinações. Se não solicitar uma combinação específica de algoritmos e parâmetros criptográficos, os gateways Azure VPN utilizam um conjunto de propostas predefinidas. Os conjuntos de políticas padrão foram escolhidos para maximizar a interoperabilidade com uma ampla gama de dispositivos VPN de terceiros em configurações padrão. Como resultado, as políticas e o número de propostas não podem abranger todas as combinações possíveis de algoritmos criptográficos disponíveis e pontos fortes fundamentais.

A definição de política padrão para gateway VpN Azure está listada no artigo: [Sobre dispositivos VPN e parâmetros IPsec/IKE para ligações de Gateway VPN site-to-site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos criptográficos

Para comunicações que requerem algoritmos ou parâmetros criptográficos específicos, normalmente devido a requisitos de conformidade ou segurança, pode agora configurar os seus gateways VpN Azure para usar uma política personalizada de IPsec/IKE com algoritmos criptográficos específicos e pontos fortes chave, em vez dos conjuntos de políticas padrão Azure.

Por exemplo, as políticas de modo principal IKEv2 para gateways Azure VPN utilizam apenas diffie-Hellman Group 2 (1024 bits), enquanto que você pode precisar especificar grupos mais fortes para serem usados em IKE, tais como Grupo 14 (2048-bit), Grupo 24 (Grupo MODP 2048-bit), ou ECP (curva elíptica curva) grupos) 256 ou 384 bits (Grupo 19 e Grupo 20, respectivamente). Requisitos semelhantes aplicam-se também às políticas de modo rápido IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política personalizada iPsec/IKE com gateways Azure VPN

Os gateways Azure VPN agora suportam por conexão, política personalizada IPsec/IKE. Para uma ligação Site-to-Site ou VNet-to-VNet, pode escolher uma combinação específica de algoritmos criptográficos para IPsec e IKE com a força chave desejada, como mostra o seguinte exemplo:

![ipsec-ike-política](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Pode criar uma política IPsec/IKE e aplicar-se a uma ligação nova ou existente.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie as redes virtuais, gateways VPN ou gateways de rede locais para a sua topologia de conectividade, conforme descrito em outros documentos de como-a-
2. Criar uma política IPsec/IKE
3. Pode aplicar a política quando criar uma ligação S2S ou VNet-to-VNet
4. Se a ligação já estiver criada, pode aplicar ou atualizar a política para uma ligação existente

## <a name="ipsecike-policy-faq"></a>FaQ política IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Passos seguintes

Consulte a [política Configure IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções passo a passo sobre a configuração da política personalizada IPsec/IKE numa ligação.

Consulte também [a Connect multiple policy-based VPN devices](vpn-gateway-connect-multiple-policybased-rm-ps.md) to learn more about the UsePolicyBasedTrafficSselect option.
