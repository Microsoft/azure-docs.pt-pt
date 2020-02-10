---
title: Sobre as conexões VPN ponto a site do Azure | Microsoft Docs
description: Este artigo ajuda você a entender as conexões ponto a site e ajuda a decidir qual tipo de autenticação de gateway de VPN P2S usar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: cherylmc
ms.openlocfilehash: 3db5cf0ddfec231a313df58e551061cbd5f9bef5
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110443"
---
# <a name="about-point-to-site-vpn"></a>Sobre VPN ponto a site

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager.

## <a name="protocol"></a>Que protocolo usa o P2S?

A VPN ponto a site pode usar um dos seguintes protocolos:

* **OpenVPN® Protocolo,** um protocolo VPN baseado em SSL/TLS. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443 de saída, que o SSL usa. O OpenVPN pode ser usado para se conectar do Android, iOS (versões 11,0 e superiores), Windows, Linux e dispositivos Mac (OSX versões 10,13 e posteriores).

* Protocolo de Túnel de Tomada Segura (SSTP), um protocolo VPN baseado em SSL. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443 de saída, que o SSL usa. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).


>[!NOTE]
>IKEv2 e OpenVPN para P2S estão disponíveis somente para o modelo de implantação do Resource Manager. Eles não estão disponíveis para o modelo de implantação clássico.
>

## <a name="authentication"></a>Como são autenticados os clientes VPN P2S?

Antes que o Azure aceite uma conexão VPN P2S, o usuário deve ser autenticado primeiro. Há dois mecanismos que o Azure oferece para autenticar um usuário que está se conectando.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticar usando a autenticação de certificado nativa do Azure

Ao usar a autenticação de certificado nativa do Azure, um certificado de cliente que está presente no dispositivo é usado para autenticar o usuário que está se conectando. Os certificados de cliente são gerados de um certificado raiz confiável e, em seguida, instalados em cada computador cliente. Você pode usar um certificado raiz que foi gerado usando uma solução corporativa ou pode gerar um certificado autoassinado.

A validação do certificado do cliente é executada pelo gateway de VPN e ocorre durante o estabelecimento da conexão VPN P2S. O certificado raiz é necessário para a validação e deve ser carregado no Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Autenticar usando a autenticação de Azure Active Directory nativa

A autenticação do Azure AD permite que os usuários se conectem ao Azure usando suas credenciais de Azure Active Directory. A autenticação AD Native Azure só é suportada para protocolo OpenVPN e Windows 10 e requer a utilização do [Cliente VPN Azure](https://go.microsoft.com/fwlink/?linkid=2117554).

Com a autenticação nativa do Azure AD, você pode aproveitar o acesso condicional do Azure AD, bem como os recursos de MFA (autenticação multifator) para VPN.

Em um alto nível, você precisa executar as seguintes etapas para configurar a autenticação do Azure AD:

1. [Configure um inquilino da AD Azure](openvpn-azure-ad-tenant.md)

2. [Ativar a autenticação da AD Azure na porta de entrada](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant#enable-authentication)

3. [Descarregue e configure o Cliente VPN Azure](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticar usando o servidor de domínio Active Directory (AD)

A autenticação de domínio do AD permite que os usuários se conectem ao Azure usando suas credenciais de domínio da organização. Ele requer um servidor RADIUS que se integre ao servidor do AD. As organizações também podem alavancar a sua implantação radius existente.   
  
O servidor RADIUS pode ser implantado localmente ou em sua VNET do Azure. Durante a autenticação, o gateway de VPN do Azure atua como uma passagem e encaminha as mensagens de autenticação entre o servidor RADIUS e o dispositivo de conexão. Portanto, a acessibilidade do gateway para o servidor RADIUS é importante. Se o servidor RADIUS estiver presente no local, então é necessária uma ligação VPN S2S do Azure ao local no local para alcançar.  
  
O servidor RADIUS também pode se integrar com os serviços de certificados do AD. Isso permite que você use o servidor RADIUS e a implantação de certificado corporativo para autenticação de certificado P2S como uma alternativa para a autenticação de certificado do Azure. A vantagem é que você não precisa carregar certificados raiz e certificados revogados no Azure.

Um servidor RADIUS também pode ser integrado a outros sistemas de identidade externos. Isso abre muitas opções de autenticação para VPN P2S, incluindo opções multifator.

![ponto-a-local](./media/point-to-site-about/p2s.png "Ponto a Site")

## <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração do cliente?

>[!NOTE]
>Para clientes Windows, você deve ter direitos de administrador no dispositivo cliente para iniciar a conexão VPN do dispositivo cliente para o Azure.
>

Os usuários usam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure fornece um arquivo zip de configuração de cliente VPN que contém as configurações exigidas por esses clientes nativos para se conectar ao Azure.

* Para dispositivos Windows, a configuração do cliente VPN consiste em um pacote do instalador que os usuários instalam em seus dispositivos.
* Para dispositivos Mac, ele consiste no arquivo mobileconfig que os usuários instalam em seus dispositivos.

O arquivo zip também fornece os valores de algumas das configurações importantes no lado do Azure que você pode usar para criar seu próprio perfil para esses dispositivos. Alguns dos valores incluem o endereço do gateway de VPN, os tipos de túnel configurados, as rotas e o certificado raiz para validação de gateway.

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

Uma configuração do P2S requer algumas etapas específicas. Os artigos a seguir contêm as etapas para orientá-lo na configuração do P2S e links para configurar os dispositivos de cliente VPN:

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configure OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a>Como fazer remover a configuração de uma conexão P2S?

Uma configuração do P2S pode ser removida usando AZ CLI e o seguinte comando: 

`az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"`
 
## <a name="faqcert"></a>FAQ para autenticação de certificado nativo Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>FAQ para autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
