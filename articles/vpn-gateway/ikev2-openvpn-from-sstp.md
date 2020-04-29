---
title: Transição para OpenVPN ou IKEv2 da SSTP Azure VPN Gateway
description: Este artigo ajuda-o a compreender as formas de ultrapassar o limite de ligação 128 simultânea da SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: alzam
ms.openlocfilehash: 5500d993a4bf3c664f14182d983f9abed8ebb08a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398368"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Transição para o protocolo OpenVPN ou IKEv2 da SSTP

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Este artigo aplica-se ao modelo de implantação do Gestor de Recursos e fala sobre formas de ultrapassar o limite de ligação 128 simultâneo do SSTP através da transição para o protocolo OpenVPN ou IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Que protocolo usa o P2S?

A VPN ponto-a-local pode utilizar um dos seguintes protocolos:

* **OpenVPN&reg; Protocol**, um protocolo VPN baseado em SSL/TLS. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o SSL utiliza. O OpenVPN pode ser utilizado para ligar a partir de dispositivos Android, iOS (versões 11.0 ou superior), Windows, Linux e Mac (versões OSX 10.13 ou superior).

* Protocolo de Túnel de **Tomada Segura (SSTP)**, um protocolo VPN baseado em SSL. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o SSL utiliza. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde). **A SSTP suporta até 128 ligações simultâneas apenas independentemente do portal SKU**.

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).


>[!NOTE]
>IKEv2 e OpenVPN para P2S estão disponíveis apenas para o modelo de implementação do Gestor de Recursos. Não estão disponíveis para o modelo de implantação clássico. Gateway Básico SKU não suporta protocolos IKEv2 ou OpenVPN. Se estiver a utilizar o SKU básico, terá de eliminar e recriar um Portal de Rede Virtual SKU de produção.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migração de SSTP para IKEv2 ou OpenVPN

Pode haver casos em que pretende suportar mais de 128 ligações p2S simultâneas a um gateway VPN, mas estão a usar SSTP. Neste caso, tem de se mudar para o protocolo IKEv2 ou OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opção 1 - Adicionar IKEv2 para além de SSTP no Gateway

Esta é a opção mais simples. SSTP e IKEv2 podem coexistir na mesma porta de entrada e dar-lhe um maior número de ligações simultâneas. Pode simplesmente ativar o IKEv2 no portal existente e rebaixar o cliente.

Adicionar o IKEv2 a um gateway VPN SSTP existente não afetará os clientes existentes e pode configurá-los para usar o IKEv2 em pequenos lotes ou apenas configurar os novos clientes para usar o IKEv2. Se um cliente windows estiver configurado tanto para sSTP como IKEv2, tentará ligar-se primeiro ao IKEV2 e se isso falhar, voltará a ser SSTP.

**O IKEv2 utiliza portas UDP não padrão, pelo que tem de garantir que estas portas não estão bloqueadas na firewall do utilizador. As portas em uso são UDP 500 e 4500.**

Para adicionar o IKEv2 a um portal existente, basta ir ao separador "configuração ponto-a-site" sob o Portal da Rede Virtual e selecionar **IKEv2 e SSTP (SSL)** a partir da caixa de lançamento.

![ponto-a-local](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opção 2 - Remova o SSTP e ative o OpenVPN no Gateway

Uma vez que o SSTP e o OpenVPN são ambos protocolos baseados em TLS, não podem coexistir na mesma porta de entrada. Se decidir afastar-se do SSTP para o OpenVPN, terá de desativar o SSTP e ativar o OpenVPN na porta de entrada. Esta operação fará com que os clientes existentes percam a conectividade com o gateway VPN até que o novo perfil tenha sido configurado no cliente.

Pode ativar o OpenVPN juntamente com o IKEv2, se desejar. O OpenVPN é baseado em TLS e utiliza a porta Padrão TCP 443. Para mudar para OpenVPN, vá ao separador "configuração ponto-a-local" sob o Portal de Rede Virtual e selecione **OpenVPN (SSL)** ou **IKEv2 e OpenVPN (SSL)** a partir da caixa de lançamento.

![ponto-a-local](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Uma vez configurada a porta de entrada, os clientes existentes não poderão ligar-se até [que implemente e configure os Clientes OpenVPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Se estiver a utilizar o Windows 10, também pode utilizar o [Cliente VPN Azure para Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração do cliente?

>[!NOTE]
>Para os clientes Windows, deve ter direitos de administrador sobre o dispositivo cliente para iniciar a ligação VPN do dispositivo cliente para o Azure.
>

Os utilizadores utilizam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure fornece um ficheiro zip de configuração de cliente VPN que contém configurações necessárias por estes clientes nativos para se conectarem ao Azure.

* Para dispositivos Windows, a configuração do cliente VPN consiste num pacote de instalação que os utilizadores instalam nos seus dispositivos.
* Para os dispositivos Mac, é composto pelo ficheiro mobileconfig que os utilizadores instalam nos seus dispositivos.

O ficheiro zip também fornece os valores de algumas das definições importantes do lado Azure que pode utilizar para criar o seu próprio perfil para estes dispositivos. Alguns dos valores incluem o endereço de gateway VPN, tipos de túneis configurados, rotas e o certificado de raiz para validação de gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Qual gateway SKUs suporta P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para recomendações gateway SKU, consulte [sobre as definições](vpn-gateway-about-vpn-gateway-settings.md#gwsku)de Gateway VPN .

>[!NOTE]
>A SKU Básica não suporta a autenticação IKEv2 ou RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Que políticas IKE/IPsec estão configuradas em gateways VPN para P2S?


**IKEv2**

|**Cifra** | **Integridade** | **PRF** | **Grupo DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Cifra** | **Integridade** | **Grupo PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Que políticas de TLS estão configuradas em gateways VPN para P2S?
**TLS**

|**Políticas** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Como configurar uma ligação P2S?

Uma configuração P2S requer alguns passos específicos. Os seguintes artigos contêm os passos para acompanhá-lo através da configuração P2S, e links para configurar os dispositivos de cliente VPN:

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar o OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Passos seguintes

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
