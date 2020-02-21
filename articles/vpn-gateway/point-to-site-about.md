---
title: Sobre ligações VPN Ponto-a-Local Azure [ Azure Point-to-Site] VPN Gateway
description: Este artigo ajuda-o a compreender as ligações Ponto-a-Local e ajuda-o a decidir qual o tipo de autenticação de gateway P2S VPN a utilizar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: cherylmc
ms.openlocfilehash: 78ed07560fdb15efb2de13c194549f5b433b775a
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500606"
---
# <a name="about-point-to-site-vpn"></a>Sobre vpn ponto-a-local

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager.

## <a name="protocol"></a>Que protocolo usa o P2S?

A VPN ponto-a-local pode utilizar um dos seguintes protocolos:

* **OpenVPN® Protocolo,** um protocolo VPN baseado em SSL/TLS. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o SSL utiliza. O OpenVPN pode ser utilizado para ligar a partir de dispositivos Android, iOS (versões 11.0 ou superior), Windows, Linux e Mac (versões OSX 10.13 ou superior).

* Protocolo de Túnel de Tomada Segura (SSTP), um protocolo VPN baseado em SSL. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o SSL utiliza. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).


>[!NOTE]
>IKEv2 e OpenVPN para P2S estão disponíveis apenas para o modelo de implementação do Gestor de Recursos. Não estão disponíveis para o modelo de implantação clássico.
>

## <a name="authentication"></a>Como são autenticados os clientes VPN P2S?

Antes de o Azure aceitar uma ligação P2S VPN, o utilizador tem de ser autenticado primeiro. Existem dois mecanismos que o Azure oferece para autenticar um utilizador de ligação.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticar usando autenticação de certificado azure nativo

Ao utilizar a autenticação do certificado Azure nativo, é utilizado um certificado de cliente que esteja presente no dispositivo para autenticar o utilizador de ligação. Os certificados de cliente são gerados a partir de um certificado de raiz fidedigno e depois instalados em cada computador cliente. Pode utilizar um certificado de raiz que foi gerado através de uma solução Enterprise, ou pode gerar um certificado auto-assinado.

A validação do certificado de cliente é realizada pelo gateway VPN e acontece durante o estabelecimento da ligação VPN P2S. O certificado de raiz é necessário para a validação e deve ser enviado para o Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Autenticar usando autenticação nativa do Diretório Ativo Azure

A autenticação Azure AD permite que os utilizadores se conectem ao Azure utilizando as suas credenciais de Diretório Ativo Azure. A autenticação AD Native Azure só é suportada para protocolo OpenVPN e Windows 10 e requer a utilização do [Cliente VPN Azure](https://go.microsoft.com/fwlink/?linkid=2117554).

Com a autenticação autóctone Azure AD, pode aproveitar o acesso condicional da Azure AD, bem como as funcionalidades de Autenticação Multi-Factor (MFA) para VPN.

A um nível elevado, é necessário realizar os seguintes passos para configurar a autenticação da AD Azure:

1. [Configure um inquilino da AD Azure](openvpn-azure-ad-tenant.md)

2. [Ativar a autenticação da AD Azure na porta de entrada](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Descarregue e configure o Cliente VPN Azure](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticar usando o Servidor de Domínio de Diretório Ativo (AD)

A autenticação do Domínio AD permite que os utilizadores se conectem ao Azure usando as suas credenciais de domínio da organização. Requer um servidor RADIUS que se integre com o servidor AD. As organizações também podem alavancar a sua implantação radius existente.
  
O servidor RADIUS pode ser implantado no local ou no seu Azure VNet. Durante a autenticação, o Azure VPN Gateway funciona como um passe e reencaminha as mensagens de autenticação para trás e para a frente entre o servidor RADIUS e o dispositivo de ligação. Por isso, a capacidade de acesso do Portal ao servidor RADIUS é importante. Se o servidor RADIUS estiver presente no local, então é necessária uma ligação VPN S2S do Azure ao local no local para alcançar.  
  
O servidor RADIUS também pode integrar-se com serviços de certificado saqueA. Isto permite-lhe utilizar o servidor RADIUS e a implementação do certificado de empresa para autenticação de certificado P2S como alternativa à autenticação do certificado Azure. A vantagem é que não precisa de enviar certificados de raiz e certificados revogados para o Azure.

Um servidor RADIUS também pode integrar-se com outros sistemas de identidade externos. Isto abre muitas opções de autenticação para P2S VPN, incluindo opções de vários fatores.

![ponto-a-local](./media/point-to-site-about/p2s.png "Ponto a Site")

## <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração do cliente?

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

## <a name="gwsku"></a>Qual gateway SKUs suporta P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para recomendações gateway SKU, consulte [sobre as definições](vpn-gateway-about-vpn-gateway-settings.md#gwsku)de Gateway VPN .

>[!NOTE]
>A SKU Básica não suporta a autenticação IKEv2 ou RADIUS.
>

## <a name="IKE/IPsec policies"></a>Que políticas IKE/IPsec estão configuradas em gateways VPN para P2S?


**IKEv2**

|**Cifra** | **Integridade** | **PRF** | **Grupo DH** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**IPsec**

|**Cifra** | **Integridade** | **Grupo PFS** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="TLS policies"></a>Que políticas de TLS estão configuradas em gateways VPN para P2S?
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

## <a name="configure"></a>Como configurar uma ligação P2S?

Uma configuração P2S requer alguns passos específicos. Os seguintes artigos contêm os passos para acompanhá-lo através da configuração P2S, e links para configurar os dispositivos de cliente VPN:

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configure OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Para remover a configuração de uma ligação P2S

Para passos, consulte as [FAQ,](#removeconfig)abaixo.
 
## <a name="faqcert"></a>FAQ para autenticação de certificado nativo Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>FAQ para autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Próximos Passos

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
