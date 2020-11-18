---
title: 'Azure VPN Gateway: Requisitos criptográficos'
description: Saiba como configurar as portas Azure VPN para satisfazer os requisitos criptográficos tanto para túneis S2S VPN como para ligações VNet-VNet.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: a77ee3c4301d278f98b571402514663dcfc9ca52
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657929"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre os requisitos criptográficos e gateways Azure VPN

Este artigo discute como pode configurar gateways Azure VPN para satisfazer os seus requisitos criptográficos tanto para túneis S2S VPN trans-premissas como para ligações VNet-to-VNet dentro do Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Sobre iKEv1 e IKEv2 para ligações Azure VPN

Tradicionalmente, permitimos ligações IKEv1 apenas para SKUs básicos e permitimos ligações IKEv2 para todos os SKUs de gateway VPN que não skus básicos. Os SKUs Básicos permitem apenas 1 ligação e, juntamente com outras limitações, como o desempenho, os clientes que usam dispositivos antigos que suportam apenas protocolos IKEv1 estavam a ter experiência limitada. De forma a melhorar a experiência dos clientes que usam protocolos IKEv1, estamos agora a permitir ligações IKEv1 para todos os SKUs de gateway VPN, exceto Basic SKU. Para mais informações, consulte [VPN Gateway SKUs](./vpn-gateway-about-vpn-gateway-settings.md#gwsku).

![Ligações Azure VPN Gateway IKEv1 e IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Quando as ligações IKEv1 e IKEv2 são aplicadas no mesmo gateway VPN, o trânsito entre estas duas ligações está ativado automaticamente.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros da política do IPsec e do IKE para gateways Azure VPN

O padrão de protocolo IPsec e IKE suporta uma ampla gama de algoritmos criptográficos em várias combinações. Se não solicitar uma combinação específica de algoritmos e parâmetros criptográficos, os gateways Azure VPN usam um conjunto de propostas padrão. Os conjuntos de políticas predefinidos foram escolhidos para maximizar a interoperabilidade com uma ampla gama de dispositivos VPN de terceiros em configurações predefinidos. Como resultado, as políticas e o número de propostas não podem abranger todas as combinações possíveis de algoritmos criptográficos disponíveis e pontos fortes fundamentais.

A política padrão definida para gateway Azure VPN está listada no artigo: [Sobre dispositivos VPN e parâmetros IPsec/IKE para ligações site-to-site VPN Gateway](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos criptográficos

Para comunicações que requerem algoritmos ou parâmetros criptográficos específicos, normalmente devidos à conformidade ou requisitos de segurança, pode agora configurar os seus gateways VPN Azure para usar uma política personalizada de IPsec/IKE com algoritmos criptográficos específicos e pontos fortes, em vez dos conjuntos de políticas padrão Azure.

Por exemplo, as políticas de modo principal IKEv2 para gateways Azure VPN utilizam apenas Diffie-Hellman Grupo 2 (1024 bits), enquanto que pode ser necessário especificar grupos mais fortes para serem utilizados no IKE, como o Grupo 14 (2048-bit), o Grupo 24 (Grupo MODP de 2048 bits), ou ECP (grupos de curvas elípticas) 256 ou 384 bits (Grupo 19 e Grupo 20, respectivamente). Requisitos semelhantes aplicam-se também às políticas de modo rápido IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política personalizada IPsec/IKE com gateways Azure VPN

As portas Azure VPN suportam agora a política IPsec/IKE personalizada por ligação. Para uma ligação Site-to-Site ou VNet-to-VNet, pode escolher uma combinação específica de algoritmos criptográficos para IPsec e IKE com a força da chave desejada, como mostra o seguinte exemplo:

![ipsec-ike-política](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Pode criar uma política IPsec/IKE e aplicar-se a uma ligação nova ou existente.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie as redes virtuais, gateways VPN ou gateways de rede locais para a sua topologia de conectividade, conforme descrito em outros documentos de como fazer
2. Criar uma política IPsec/IKE
3. Pode aplicar a política quando criar uma ligação S2S ou VNet-to-VNet
4. Se a ligação já estiver criada, pode aplicar ou atualizar a política para uma ligação existente

## <a name="ipsecike-policy-faq"></a>Política IPsec/IKE FAQ

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Próximos passos

Consulte [a política de configuração IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para instruções passo a passo sobre a configuração da política personalizada IPsec/IKE numa ligação.

Consulte também [vários dispositivos VPN baseados em políticas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre a opção UsePolicyBasedTrafficSelectors.