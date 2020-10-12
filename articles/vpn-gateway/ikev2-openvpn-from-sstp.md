---
title: Transição para OpenVPN ou IKEv2 da SSTP Azure VPN Gateway
description: Este artigo ajuda-o a compreender as formas de ultrapassar o limite de ligação simultânea de 128 SSTP.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: c7f71d24ab516044a0ce48ad40f78bc659268866
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442179"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Transição para protocolo OpenVPN ou IKEv2 da SSTP

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Este artigo aplica-se ao modelo de implementação do Gestor de Recursos e fala sobre formas de ultrapassar o limite de ligação simultânea de 128 SSTP através da transição para o protocolo OpenVPN ou IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Que protocolo utiliza o P2S?

A VPN ponto-a-local pode utilizar um dos seguintes protocolos:

* **OpenVPN &reg; Protocolo**, protocolo VPN baseado em SSL/TLS. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que a SSL utiliza. O OpenVPN pode ser utilizado para ligar a partir de dispositivos Android, iOS (versões 11.0 e superior), dispositivos Windows, Linux e Mac (versões OSX 10.13 ou superiores).

* **Secure Socket Tunneling Protocol (SSTP)**, um protocolo VPN baseado em SSL. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que a SSL utiliza. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde). **A SSTP suporta até 128 ligações simultâneas apenas independentemente do gateway SKU**.

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).


>[!NOTE]
>IKEv2 e OpenVPN para P2S estão disponíveis apenas para o modelo de implementação do Gestor de Recursos. Não estão disponíveis para o modelo clássico de implantação. O gateway básico SKU não suporta protocolos IKEv2 ou OpenVPN. Se estiver a utilizar o SKU básico, terá de eliminar e recriar uma produção SKU Virtual Network Gateway.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migração de SSTP para IKEv2 ou OpenVPN

Pode haver casos em que pretender suportar mais de 128 ligações P2S simultâneas a uma porta de entrada VPN, mas estão a usar SSTP. Neste caso, tem de se mudar para o protocolo IKEv2 ou OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opção 1 - Adicionar IKEv2 para além do SSTP no Gateway

Esta é a opção mais simples. SSTP e IKEv2 podem coexistir no mesmo portal e dar-lhe um maior número de ligações simultâneas. Pode simplesmente ativar o IKEv2 no gateway existente e recarregar o cliente.

Adicionar o IKEv2 a um gateway SSTP VPN existente não afetará os clientes existentes e pode configurá-los para usar o IKEv2 em pequenos lotes ou apenas configurar os novos clientes para usar o IKEv2. Se um cliente Windows estiver configurado tanto para o SSTP como para o IKEv2, tentará ligar primeiro o IKEV2 e, se isso falhar, voltará a ser O SSTP.

**O IKEv2 utiliza portas UDP não standard, pelo que é necessário garantir que estas portas não estão bloqueadas na firewall do utilizador. As portas em uso são UDP 500 e 4500.**

Para adicionar O IKEv2 a um gateway existente, basta ir ao separador "configuração ponto-a-local" no portal virtual Network Gateway e selecionar **IKEv2 e SSTP (SSL)** a partir da caixa de entrega.

![Screenshot que mostra a página "Configuração ponto-a-local" com o "tipo de túnel" aberto, e "IKEv2 e SSTP(SSTP)" selecionados.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opção 2 - Remover SSTP e ativar o OpenVPN no Gateway

Uma vez que o SSTP e o OpenVPN são ambos protocolo baseado em TLS, não podem coexistir no mesmo portal. Se decidir afastar-se do SSTP para o OpenVPN, terá de desativar o SSTP e ativar o OpenVPN no gateway. Esta operação fará com que os clientes existentes percam conectividade com a porta de entrada VPN até que o novo perfil tenha sido configurado no cliente.

Pode ativar o OpenVPN juntamente com o IKEv2, se desejar. OpenVPN é baseado em TLS e utiliza a porta padrão TCP 443. Para mudar para OpenVPN, aceda ao separador "configuração ponto-a-local" no portal virtual Network Gateway e selecione **OpenVPN (SSL)** ou **IKEv2 e OpenVPN (SSL)** a partir da caixa de entrega.

![ponto a local](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Uma vez configurado o gateway, os clientes existentes não poderão ligar-se até [que implemente e configure os Clientes OpenVPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)

Se estiver a utilizar o Windows 10, também pode utilizar o [Cliente Azure VPN para windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-are-the-client-configuration-requirements"></a>What are the client configuration requirements? (Quais são os requisitos de configuração do cliente?)

>[!NOTE]
>Para os clientes windows, deve ter direitos de administrador no dispositivo cliente para iniciar a ligação VPN do dispositivo cliente para a Azure.
>

Os utilizadores utilizam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure fornece um ficheiro zip de configuração de configuração do cliente VPN que contém as configurações exigidas por estes clientes nativos para se conectarem ao Azure.

* Para dispositivos Windows, a configuração do cliente VPN consiste num pacote instalador que os utilizadores instalam nos seus dispositivos.
* No caso dos dispositivos Mac, é composto pelo ficheiro mobileconfig que os utilizadores instalam nos seus dispositivos.

O ficheiro zip também fornece os valores de algumas das configurações importantes do lado Azure que pode utilizar para criar o seu próprio perfil para estes dispositivos. Alguns dos valores incluem o endereço de gateway VPN, tipos de túneis configurados, rotas e o certificado de raiz para validação de gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Qual porta de entrada SKUs suporta P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para recomendações do Gateway SKU, consulte [as definições do Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

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

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Que políticas TLS estão configuradas em gateways VPN para P2S?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Como posso configurar uma ligação P2S?

Uma configuração P2S requer alguns passos específicos. Os seguintes artigos contêm os passos para o percorrer através da configuração P2S, e ligam-se para configurar os dispositivos clientes VPN:

* [Configure uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - Autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar o OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Passos seguintes

* [Configure uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - Autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
