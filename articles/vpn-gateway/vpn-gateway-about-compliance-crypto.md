---
title: 'Gateway de VPN do Azure: requisitos de criptografia'
description: Este artigo discute os requisitos criptográficos e os gateways de VPN do Azure
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902830"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre requisitos de criptografia e gateways de VPN do Azure

Este artigo discute como você pode configurar gateways de VPN do Azure para atender aos seus requisitos de criptografia para túneis de VPN S2S entre locais e conexões de VNet para VNet no Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Sobre IKEv1 e IKEv2 para conexões VPN do Azure

Tradicionalmente, permitimos conexões IKEv1 somente para SKUs básicas e conexões IKEv2 permitidas para todas as SKUs de gateway de VPN diferentes de SKUs básicas. Os SKUs básicos permitem apenas uma conexão e, juntamente com outras limitações, como desempenho, os clientes que usam dispositivos herdados que dão suporte apenas a protocolos IKEv1 têm experiência limitada. Para aprimorar a experiência de clientes que usam protocolos IKEv1, agora estamos permitindo conexões IKEv1 para todas as SKUs de gateway de VPN, exceto SKU básico. Para obter mais informações, consulte [SKUs de gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Conexões IKEv1 e IKEv2 do gateway de VPN do Azure](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Quando as conexões IKEv1 e IKEv2 são aplicadas ao mesmo gateway de VPN, o trânsito entre essas duas conexões é habilitado automaticamente.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros de política IKE e IPsec para gateways de VPN do Azure

O IPsec e o protocolo IKE Standard dão suporte a uma ampla gama de algoritmos criptográficos em várias combinações. Se você não solicitar uma combinação específica de parâmetros e algoritmos de criptografia, os gateways de VPN do Azure usarão um conjunto de propostas padrão. Os conjuntos de políticas padrão foram escolhidos para maximizar a interoperabilidade com uma ampla variedade de dispositivos VPN de terceiros nas configurações padrão. Como resultado, as políticas e o número de propostas não podem abranger todas as combinações possíveis de algoritmos criptográficos e forças de chave disponíveis.

A política padrão definida para o gateway de VPN do Azure está listada no artigo: [sobre dispositivos VPN e parâmetros de IPSec/IKE para conexões de gateway de VPN site a site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos de criptografia

Para comunicações que exigem algoritmos ou parâmetros criptográficos específicos, normalmente devido a requisitos de conformidade ou de segurança, agora você pode configurar seus gateways de VPN do Azure para usar uma política de IPsec/IKE personalizada com algoritmos de criptografia e forças de chave específicos, em vez dos conjuntos de políticas padrão do Azure.

Por exemplo, as políticas de modo principal IKEv2 para gateways de VPN do Azure utilizam apenas o grupo Diffie-Hellman 2 (1024 bits), enquanto talvez seja necessário especificar grupos mais fortes a serem usados no IKE, como o grupo 14 (2048 bits), grupo 24 (grupo de MODP bits de 2048 bits) ou ECP (curva elíptica grupos) 256 ou 384 bits (grupo 19 e grupo 20, respectivamente). Requisitos semelhantes também se aplicam às políticas de modo rápido do IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política de IPsec/IKE personalizada com gateways de VPN do Azure

Os gateways de VPN do Azure agora dão suporte à política de IPsec/IKE personalizada por conexão. Para uma conexão site a site ou VNet a VNet, você pode escolher uma combinação específica de algoritmos de criptografia para IPsec e IKE com a restrição de chave desejada, conforme mostrado no exemplo a seguir:

![IPSec-IKE-política](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Você pode criar uma política de IPsec/IKE e aplicar a uma conexão nova ou existente.

### <a name="workflow"></a>Fluxo de Trabalho

1. Crie as redes virtuais, gateways de VPN ou gateways de rede local para sua topologia de conectividade, conforme descrito em outros documentos de instruções
2. Criar uma política de IPsec/IKE
3. Você pode aplicar a política ao criar uma conexão S2S ou VNet para VNet
4. Se a conexão já tiver sido criada, você poderá aplicar ou atualizar a política para uma conexão existente

## <a name="ipsecike-policy-faq"></a>Perguntas frequentes sobre política de IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Passos seguintes

Consulte [Configurar a política de IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções passo a passo sobre como configurar a política de IPSec/IKE personalizada em uma conexão.

Consulte também [conectar vários dispositivos VPN com base em políticas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre a opção UsePolicyBasedTrafficSelectors.
