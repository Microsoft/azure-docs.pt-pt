---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6684c4269f14b91ded651dadff3f0a2e0878a4f2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562789"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>A política de IPsec/IKE personalizado é suportada em todos os SKU de Gateway de VPN do Azure?
A política personalizada IPsec/IKE é suportada em todos os SKUs Azure, exceto no SKU Básico.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Quantas políticas posso especificar numa ligação?
Só pode especificar ***uma** _ combinação de política para uma determinada ligação.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Posso especificar uma política parcial numa ligação? (por exemplo, apenas os algoritmos de IKE, mas não IPsec)
Não, tem de especificar todos os parâmetros e algoritmos de IKE (modo principal) e IPsec (modo rápido). Não é permitida a especificação da política parcial.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Quais são os algoritmos e os principais pontos fortes suportados na política personalizada?
A tabela a seguir lista os algoritmos de criptográficos suportados e os principais pontos fortes configuráveis pelos clientes. Tem de selecionar uma opção para cada campo.

| _ *IPsec/IKEv2**  | **Opções**                                                                   |
| ---              | ---                                                                           |
| Encriptação IKEv2 | AES256, AES192, AES128, DES3, DES                                             |
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5                                                     |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, Nenhum  |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum      |
| Integridade do IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nenhum                              |
| Duração de SA QM   | Segundos (número inteiro; **mín. 300**/predefinição de 27000 segundos)<br>KBytes (número inteiro; **mín. 1024**/predefinição de 102400000 KBytes)           |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors ($True/$False; default $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 & PFS2048 são iguais ao Grupo Diffie-Hellman **14** em IKE e IPsec PFS. Veja os [Grupos de Diffie-Hellman](#DH) para obter os mapeamentos completos.
> 2. Para algoritmos GCMAES, tem de especificar o mesmo algoritmo GCMAES e o comprimento da chave de encriptação de IPsec e da Integridade.
> 3. A vida útil do Modo Principal IKEv2 SA é fixada a 28.800 segundos nos gateways Azure VPN.
> 4. As Durações de SA QM são parâmetros opcionais. Se tiver sido especificado “nenhum”, são utilizados os valores predefinidos de 27,000 segundos (7,5 horas) e de 102400000 KBytes (102 GB).
> 5. UsePolicyBasedTrafficSelector é um parâmetro de opção da ligação. Veja o item seguinte das FAQ relativamente a "UsePolicyBasedTrafficSelectors"

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Tudo o que precisa para fazer a correspondência entre a política de gateway VPN do Azure e as minhas configurações de dispositivo VPN local?
A configuração de dispositivo VPN local deve corresponder ou deve conter os seguintes algoritmos e parâmetros que especificar na política de IPsec/IKE do Azure:

* Algoritmo de encriptação do IKE
* Algoritmo de integridade do IKE
* Grupo DH
* Algoritmo de encriptação do IPsec
* Algoritmo de integridade do IPsec
* Grupo PFS
* Seletor de tráfego (*)

A duração do SA é apenas a especificação local, não é necessário corresponder.

Se ativar **UsePolicyBasedTrafficSelectors**, tem de garantir que o dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações dos prefixos de rede (gateway de rede local) no local de/para os prefixos de rede virtual do Azure, em vez de qualquer um para qualquer um. Por exemplo, se os prefixos de rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos de rede virtual são 192.168.0.0/16 e 172.16.0.0/16, tem de especificar os seletores de tráfego seguintes:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Para obter mais informações, veja [Connect multiple on-premises policy-based VPN devices](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Ligar vários dispositivos VPN baseados em políticas no local).

### <a name="which-diffie-hellman-groups-are-supported"></a><a name ="DH"></a>Que Grupos de Diffie-Hellman são suportados?
A tabela abaixo lista os Grupos de Diffie-Hellman suportados para IKE (DHGroup) e IPsec (PFSGroup):

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP384       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |
|                           |                          |              |                |

Para obter mais informações, veja [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>A política personalizada substitui os conjuntos de políticas predefinidos de IPsec/IKE para gateways de VPN do Azure?
Sim, quando uma política personalizada é especificada numa ligação, o gateway de VPN do Azure só irá usar a política na ligação, como iniciador IKE e dispositivo de resposta IKE.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Se remover uma política de IPsec/IKE personalizada, a ligação torna-se desprotegida?
Não, a ligação ainda estará protegida pelo IPsec/IKE. Quando remove a política personalizada de uma ligação, o gateway de VPN do Azure reverte para a [lista predefinida de propostas de IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) e reinicia o handshake IKE novamente com o dispositivo VPN local.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Adicionar ou atualizar uma política de IPsec/IKE pode atrapalhar a minha ligação VPN?
Sim, pode causar uma pequena interrupção (alguns segundos), pois o gateway de VPN do Azure quebra a ligação existente e reinicia o handshake IKE para restabelecer o túnel IPsec com os novos parâmetros e algoritmos criptográficos. Verifique se o dispositivo VPN local também é configurado com os algoritmos correspondentes e os principais pontos fortes para minimizar a interrupção.

### <a name="can-i-use-different-policies-on-different-connections"></a>Posso usar políticas diferentes em ligações diferentes?
Sim. A política personalizada é aplicada consoante a ligação. Pode criar e aplicar diferentes políticas de IPsec/IKE em diferentes ligações. Também pode optar por aplicar políticas personalizadas num subconjunto de ligações. As restantes utilizam os conjuntos de políticas predefinidas de IPsec/IKE do Azure.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Também posso usar a política personalizada na ligação de VNet para VNet?
Sim, pode aplicar a política personalizada em ligações entre locais de IPsec ou em ligações VNet para VNet.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>É necessário especificar a mesma política em ambos os recursos de ligação de VNet para VNet?
Sim. Um túnel de VNet para VNet consiste em dois recursos de ligação no Azure, uma para cada direção. Confirme que os recursos de ligação têm a mesma política, senão a ligação VNet para VNet não é estabelecida.

### <a name="what-is-the-default-dpd-timeout-value-can-i-specify-a-different-dpd-timeout"></a>Qual é o valor de tempo limite padrão da DPD? Posso especificar um tempo limite diferente?
O tempo limite padrão do DPD é de 45 segundos. Pode especificar um valor de tempo DPD diferente em cada ligação IPsec ou VNet-to-VNet entre 9 segundos e 3600 segundos.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>A política de IPsec/IKE personalizada funciona na ligação do ExpressRoute?
Não. A política de IPsec/IKE só funciona em ligações VPN S2S e VNet para VNet por meio de gateways de VPN do Azure.

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>Como posso criar ligações com o tipo de protocolo IKEv1 ou IKEv2?
As ligações IKEv1 podem ser criadas em todas as SKUs do tipo VPN routebased, com exceção do SKU Básico, SKU padrão e [outros SKUs legados.](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#gwsku) Pode especificar um tipo de protocolo de ligação de IKEv1 ou IKEv2 enquanto cria ligações. Se não especificar um tipo de protocolo de ligação, o IKEv2 é utilizado como opção padrão, quando aplicável. Para obter mais informações, consulte a documentação [do cmdlet PowerShell.](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) Para os tipos SKU e suporte IKEv1/IKEv2, consulte [as portas de ligação para dispositivos VPN baseados em políticas](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>O trânsito entre as ligações IKEv1 e IKEv2 é permitido?
Sim. O trânsito entre as ligações IKEv1 e IKEv2 é suportado.

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>Posso ter ligações iKEv1 site-to-site em SKUs Básicos do tipo VPN RouteBased?
Não. O SKU Básico não apoia isto.

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>Posso alterar o tipo de protocolo de ligação após a criação da ligação (IKEv1 para IKEv2 e vice-versa)?
Não. Uma vez criada a ligação, os protocolos IKEv1/IKEv2 não podem ser alterados. Deve eliminar e recriar uma nova ligação com o tipo de protocolo pretendido.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Onde posso encontrar mais informações de configuração para o IPsec?
Consulte [a política de Configuração IPsec/IKE para ligações S2S ou VNet-to-VNet](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)