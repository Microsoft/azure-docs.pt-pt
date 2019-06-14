---
title: Sobre os requisitos criptográficos e gateways de VPN do Azure | Documentos da Microsoft
description: Este artigo aborda os requisitos criptográficos e gateways de VPN do Azure
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648692"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre os requisitos criptográficos e gateways de VPN do Azure

Este artigo aborda como configurar gateways de VPN do Azure para satisfazer os requisitos de criptografia para túneis de S2S VPN em vários locais e ligações de VNet a VNet no Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros de política de IPsec e IKE para gateways de VPN do Azure
Protocolo IPsec e IKE standard suporta uma vasta gama de algoritmos criptográficos em várias combinações. Se os clientes não pedem uma combinação de parâmetros e algoritmos criptográficos, gateways de VPN do Azure utilizam um conjunto de propostas de predefinição. Os conjuntos de políticas de predefinição foram escolhidos para maximizar a interoperabilidade com uma vasta gama de dispositivos VPN de terceiros em configurações predefinidas. Como resultado, as políticas e o número de propostas de não abrangem todas as possíveis combinações de algoritmos criptográficos disponíveis e os principais pontos fortes.

A predefinição da política definida para o gateway de VPN do Azure está listada no documento: [Sobre dispositivos VPN e parâmetros IPsec/IKE para ligações de Gateway de VPN de Site a Site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos de criptografia
Para comunicações que necessitam de algoritmos de criptografia específicos ou parâmetros, normalmente devido a conformidade ou requisitos de segurança, os clientes podem agora configurar seus gateways de VPN do Azure para utilizar uma política de IPsec/IKE personalizada com específicos de criptografia algoritmos e principais pontos fortes, em vez dos conjuntos de políticas padrão do Azure.

Por exemplo, as políticas de modo principal do IKEv2 para gateways de VPN do Azure utilizam apenas grupo Diffie-Hellman de 2 (1024 bits), ao passo que os clientes poderão ter de especificar grupos mais fortes para ser usado em IKE, como (2048 bits) do grupo de 14, 24 de grupo (grupo de MODP de 2048 bits) ou ECP (elíptica curva grupos) 256 ou 384 bits (grupo 19 e 20 de grupo, respectivamente). Semelhantes requisitos aplicam-se às políticas de modo rápido de IPsec também.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política de IPsec/IKE personalizada com gateways de VPN do Azure
Os gateways VPN do Azure suportam agora por conexão, política de IPsec/IKE personalizada. Para um Site a Site ou a ligação de VNet a VNet, pode escolher uma combinação de algoritmos criptográficos para IPsec e IKE com a força da chave desejada, conforme mostrado no exemplo a seguir:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Pode criar uma política de IPsec/IKE e aplicam-se a uma ligação nova ou existente. 

### <a name="workflow"></a>Fluxo de trabalho

1. Criar as redes virtuais, gateways de VPN ou gateways de rede local para a sua topologia de conectividade, conforme descrito em outros documentos de procedimentos
2. Criar uma política de IPsec/IKE
3. Pode aplicar a política ao criar uma ligação S2S ou VNet a VNet
4. Se a ligação já está a ser criada, pode aplicar ou atualizar a política para uma ligação existente


## <a name="ipsecike-policy-faq"></a>FAQ da política de IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Passos Seguintes
Ver [política de IPsec/IKE configurar](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções passo a passo sobre como configurar a política de IPsec/IKE personalizada numa conexão.

Consulte também [ligar vários dispositivos VPN baseado em políticas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre a opção de UsePolicyBasedTrafficSelectors.