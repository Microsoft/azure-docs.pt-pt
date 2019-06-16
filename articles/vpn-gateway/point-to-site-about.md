---
title: Acerca das ligações de VPN do Azure ponto a Site | Documentos da Microsoft
description: Este artigo ajuda-o a compreender as ligações ponto a Site e ajuda-o a decidir o tipo de autenticação de gateway de P2S VPN a utilizar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: cherylmc
ms.openlocfilehash: f1e014bb14b2b5c1ae924f4371e08aa8bf8698f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056492"
---
# <a name="about-point-to-site-vpn"></a>Acerca da VPN ponto a Site

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager.

## <a name="protocol"></a>Qual protocolo usar P2S?

VPN Point-to-site pode utilizar um dos seguintes protocolos:

* **Protocolo de® OpenVPN**, um SSL/TLS com protocolo VPN base. Uma solução de SSL VPN consegue penetrar firewalls, uma vez que a maioria das firewalls abre a porta TCP 443 de saída, que usa SSL. OpenVPN pode ser utilizado para ligar a partir do Android, iOS (versões 11.0 e posterior), dispositivos Windows, Linux e Mac (versões OSX 10.13 e acima).

* Secure Socket Tunneling Protocol (SSTP), um protocolo proprietário de VPN baseado em SSL. Uma solução de SSL VPN consegue penetrar firewalls, uma vez que a maioria das firewalls abre a porta TCP 443 de saída, que usa SSL. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que têm de SSTP (Windows 7 e posterior).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).


>[!NOTE]
>IKEv2 e OpenVPN para P2S estão disponíveis para apenas o modelo de implementação do Resource Manager. Não estão disponíveis para o modelo de implementação clássica.
>

## <a name="authentication"></a>Como é que os clientes VPN de P2S são são autenticados?

Antes de Azure aceita uma ligação P2S VPN, o utilizador tem de ser autenticada pela primeira vez. Há dois mecanismos que o Azure oferece para autenticar um usuário conectado.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticar através da autenticação de certificados nativa do Azure

Ao utilizar a autenticação de certificados nativa do Azure, um certificado de cliente que está presente no dispositivo é utilizado para autenticar o usuário conectado. Certificados de cliente são gerados a partir de um certificado de raiz fidedigna e, em seguida, instalados em cada computador cliente. Pode utilizar um certificado de raiz que tenha sido gerado com uma solução empresarial ou pode gerar um certificado autoassinado.

A validação do certificado de cliente é executada pelo gateway de VPN e ocorre durante o estabelecimento da conexão VPN de P2S. O certificado de raiz é necessário para a validação e tem de ser carregado para o Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticar com o servidor de domínio do Active Directory (AD)

Autenticação de domínio do AD permite aos utilizadores ligar ao Azure através das respetivas credenciais de domínio da organização. Ele requer um servidor RADIUS que se integra com o servidor do AD. As organizações também podem aproveitar a implantação de RADIUS existente.   
  
O servidor RADIUS pode ser implementado no local ou na sua VNET do Azure. Durante a autenticação, o Gateway de VPN do Azure funciona como um pass-through e encaminha as mensagens de autenticação e volta entre o servidor RADIUS e o dispositivo de ligação. Portanto, a acessibilidade de Gateway para o servidor RADIUS é importante. Se o servidor RADIUS estiver presente no local, em seguida, uma ligação de VPN S2S do Azure para o site no local é necessária para acessibilidade.  
  
O servidor RADIUS também pode integrar com serviços de certificados do AD. Isto permite-lhe utilizar o servidor RADIUS e sua implementação do certificado de empresa para autenticação de certificados P2S como uma alternativa para a autenticação de certificados do Azure. A vantagem é que não é necessário carregar certificados de raiz e certificados revogados para o Azure.

Um servidor RADIUS também pode integrar com outros sistemas de identidade externo. Isso abre muitas opções de autenticação para VPN P2S, incluindo opções de acesso multifatores.

>[!NOTE]
>**Protocolo de® OpenVPN** não é suportado com a autenticação RADIUS.
>

![point-to-site](./media/point-to-site-about/p2s.png "Point-to-Site")

## <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração de cliente?

>[!NOTE]
>Para clientes do Windows, tem de ter direitos de administrador no dispositivo cliente para iniciar a ligação de VPN do dispositivo cliente para o Azure.
>

Os utilizadores utilizam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure disponibiliza um cliente VPN zip de configuração ficheiro que contém as definições necessárias por estes clientes nativos para ligar ao Azure.

* Para dispositivos Windows, a configuração do cliente VPN é composta por um pacote de instalador que os usuários instalam em seus dispositivos.
* Para dispositivos Mac, ele consiste no ficheiro de mobileconfig que os usuários instalam em seus dispositivos.

O ficheiro zip também fornece os valores de algumas das definições importantes no lado do Azure que pode utilizar para criar seu próprio perfil para estes dispositivos. Alguns dos valores incluem o endereço de gateway de VPN, os tipos de túnel configurado, as rotas e o certificado de raiz para validação de gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>O suporte de SKUs de gateway P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para obter recomendações de SKU de Gateway, veja [definições sobre o Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>A SKU Básica não suporta a autenticação IKEv2 ou RADIUS.
>

## <a name="IKE/IPsec policies"></a>Que políticas de IKE/IPsec estão configuradas em gateways de VPN para P2S?


**IKEv2**

|**Cifras** | **Integridade** | **PRF** | **Grupo DH** |
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

|**Cifras** | **Integridade** | **Grupo PFS** |
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

## <a name="TLS policies"></a>Que políticas TLS estão configuradas em gateways de VPN para P2S?
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




## <a name="configure"></a>Como posso configurar uma ligação P2S?

Uma configuração P2S requer algumas etapas específicas. Os artigos seguintes contêm as etapas para orientá-lo através da configuração de P2S e links para configurar os dispositivos de cliente VPN:

* [Configurar uma ligação de P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação de P2S - autenticação de certificados nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a>Como posso remover a configuração de uma ligação P2S?

Uma configuração P2S pode ser removida com az cli e o seguinte comando: 

`az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"`
 
## <a name="faqcert"></a>FAQ sobre a autenticação de certificados nativa do Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>FAQ sobre a autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Próximos Passos

* [Configurar uma ligação de P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação de P2S - autenticação de certificados nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**
