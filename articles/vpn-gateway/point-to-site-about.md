---
title: Sobre as ligações VPN ponto-a-local do Azure VPN Gateway
description: Este artigo ajuda-o a compreender as ligações Ponto-a-Local e ajuda-o a decidir qual tipo de autenticação de gateway P2S VPN a utilizar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 795b6f13913590041b463115c0be65a6201fedab
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654069"
---
# <a name="about-point-to-site-vpn"></a>Sobre a VPN ponto-a-local

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Que protocolo utiliza o P2S?

A VPN ponto-a-local pode utilizar um dos seguintes protocolos:

* **OpenVPN® Protocol**, um protocolo VPN baseado em SSL/TLS. Uma solução TLS VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443 de saída, que o TLS utiliza. O OpenVPN pode ser utilizado para ligar a partir de dispositivos Android, iOS (versões 11.0 e superior), dispositivos Windows, Linux e Mac (versões OSX 10.13 ou superiores).

* Secure Socket Tunneling Protocol (SSTP), um protocolo VPN baseado em TLS. Uma solução TLS VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443 de saída, que o TLS utiliza. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).


>[!NOTE]
>IKEv2 e OpenVPN para P2S estão disponíveis apenas para o modelo de implementação do Gestor de Recursos. Não estão disponíveis para o modelo clássico de implantação.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Como são autenticados os clientes P2S VPN?

Antes de a Azure aceitar uma ligação P2S VPN, o utilizador tem de ser autenticado primeiro. Existem dois mecanismos que a Azure oferece para autenticar um utilizador de ligação.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticar utilizando a autenticação de certificado azure nativo

Ao utilizar a autenticação do certificado Azure nativo, é utilizado um certificado de cliente presente no dispositivo para autenticar o utilizador de ligação. Os certificados de cliente são gerados a partir de um certificado de raiz fidedigno e depois instalados em cada computador cliente. Pode utilizar um certificado de raiz que foi gerado usando uma solução Enterprise, ou pode gerar um certificado auto-assinado.

A validação do certificado de cliente é realizada pelo gateway VPN e acontece durante o estabelecimento da ligação P2S VPN. O certificado de raiz é necessário para a validação e deve ser enviado para a Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Autenticar utilizando a autenticação nativa do Azure Ative Directory

A autenticação AZure AD permite que os utilizadores se conectem ao Azure usando as suas credenciais de Diretório Azure Ative. A autenticação AZure AD nativa é suportada apenas para o protocolo OpenVPN e Windows 10 e requer a utilização do [Cliente Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554).

Com a autenticação AZure AD nativa, pode aproveitar o acesso condicional do Azure AD, bem como as funcionalidades de Autenticação Multi-Factor (MFA) para VPN.

A um nível elevado, você precisa executar os seguintes passos para configurar a autenticação AZure AD:

1. [Configurar um inquilino do Azure AD](openvpn-azure-ad-tenant.md)

2. [Ativar a autenticação AD AD no gateway](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Baixar e configurar cliente Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticar usando o Servidor de Domínio ativo (AD)

A autenticação do Domínio AD permite que os utilizadores se conectem ao Azure utilizando as suas credenciais de domínio de organização. Requer um servidor RADIUS que se integre com o servidor AD. As organizações também podem aproveitar a implantação do RADIUS existente.
  
O servidor RADIUS pode ser implantado no local ou no seu Azure VNet. Durante a autenticação, o Gateway Azure VPN atua como uma passagem e encaminha mensagens de autenticação para trás e para a frente entre o servidor RADIUS e o dispositivo de ligação. Assim, a capacidade de acesso do Gateway para o servidor RADIUS é importante. Se o servidor RADIUS estiver presente no local, é necessária uma ligação VPN S2S de Azure para o local no local para alcançar.  
  
O servidor RADIUS também pode integrar-se com os serviços de certificados AD. Isto permite-lhe utilizar o servidor RADIUS e a implementação do certificado da empresa para a autenticação de certificadoS P2S como alternativa à autenticação do certificado Azure. A vantagem é que não precisa de carregar certificados de raiz e revogar certificados para a Azure.

Um servidor RADIUS também pode integrar-se com outros sistemas de identidade externos. Isto abre muitas opções de autenticação para P2S VPN, incluindo opções multi-factor.

![Diagrama que mostra uma VPN ponto-a-local com um local no local.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>What are the client configuration requirements? (Quais são os requisitos de configuração do cliente?)

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

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Qual porta de entrada SKUs suporta P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para recomendações do Gateway SKU, consulte [as definições do Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>A SKU Básica não suporta a autenticação IKEv2 ou RADIUS.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Que políticas IKE/IPsec estão configuradas em gateways VPN para P2S?


**IKEv2**

| **Cifra** | **Integridade** | **PRF** | **Grupo DH** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Cifra** | **Integridade** | **Grupo PFS** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Que políticas TLS estão configuradas em gateways VPN para P2S?
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

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Como posso configurar uma ligação P2S?

Uma configuração P2S requer alguns passos específicos. Os seguintes artigos contêm os passos para o percorrer através da configuração P2S, e ligam-se para configurar os dispositivos clientes VPN:

* [Configure uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - Autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar o OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Para remover a configuração de uma ligação P2S

Para passos, consulte as [FAQ,](#removeconfig)abaixo.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>FAQ para autenticação de certificado azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>FAQ para autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Configure uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - Autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
