---
title: 'Trabalho remoto com P2S: Azure VPN Gateway'
description: Esta página descreve como pode aproveitar o Gateway VPN para ativar o funcionamento remotamente devido à pandemia COVID-19.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880089"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Trabalho remoto usando Azure VPN Gateway Point-to-site

>[!NOTE]
>Este artigo descreve como pode aproveitar a Azure VPN Gateway, Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

Este artigo descreve as opções que estão disponíveis para as organizações para estabelecer acesso remoto para os seus utilizadores ou para complementar as suas soluções existentes com capacidade adicional durante a epidemia COVID-19.

A solução Azure ponto-a-local é baseada em nuvem e pode ser a provisionada rapidamente para atender à procura crescente de utilizadores para trabalhar a partir de casa. Pode escalar facilmente e desligar-se com a mesma facilidade e rapidez quando a capacidade aumentada já não é necessária.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Sobre a VPN ponto-a-local

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para telecomuters que queiram ligar-se a VNets Azure ou centros de dados no local a partir de um local remoto, como a partir de casa ou de uma conferência. Este artigo descreve como permitir que os utilizadores trabalhem remotamente com base em vários cenários.

A tabela abaixo mostra os sistemas operativos do cliente e as opções de autenticação que estão disponíveis para os mesmos. Seria útil selecionar o método de autenticação com base no sistema operativo cliente que já está em uso. Por exemplo, selecione OpenVPN com autenticação baseada em Certificado se tiver uma mistura de sistemas operativos do cliente que precisam de se ligar. Além disso, por favor, note que a VPN ponto-a-local só é suportada em gateways VPN baseados em rotas.

![Screenshot que mostra sistemas operativos do cliente e opções de autenticação disponíveis.](./media/working-remotely-support/ostable.png "SO")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Cenário 1 - Os utilizadores precisam de acesso apenas a recursos em Azure

Neste cenário, os utilizadores remotos apenas precisam de ter acesso aos recursos que se encontram no Azure.

![Diagrama que mostra um cenário ponto a local para utilizadores que precisam de acesso a recursos apenas em Azure.](./media/working-remotely-support/scenario1.png "Cenário 1")

A um nível elevado, são necessários os seguintes passos para permitir que os utilizadores se conectem com segurança aos recursos Azure:

1. Criar um gateway de rede virtual (se não existir).
2. Configure a VPN ponto-a-local no gateway.

   * Para autenticação de certificados, siga [este link](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Para o OpenVPN, siga [este link](vpn-gateway-howto-openvpn.md).
   * Para a autenticação AZure AD, siga [este link](openvpn-azure-ad-tenant.md).
   * Para a resolução de problemas das ligações ponto a local, siga [este link](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Descarregue e distribua a configuração do cliente VPN.
4. Distribuir os certificados (se a autenticação do certificado for selecionada) aos clientes.
5. Ligue-se à Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Cenário 2 - Os utilizadores precisam de acesso a recursos em recursos Azure e/ou on-prem

Neste cenário, os utilizadores remotos precisam de ter acesso aos recursos que se encontram no Azure e nos centros de dados das instalações.

![Diagrama que mostra um cenário ponto a local para os utilizadores que precisam de acesso aos recursos em Azure.](./media/working-remotely-support/scenario2.png "Cenário 2")

A um nível elevado, são necessários os seguintes passos para permitir que os utilizadores se conectem com segurança aos recursos Azure:

1. Criar um gateway de rede virtual (se não existir).
2. Configure a VPN ponto-a-local no gateway (ver [Cenário 1](#scenario1)).
3. Configure um túnel local-local na porta de entrada de rede virtual Azure com BGP ativado.
4. Configure o dispositivo no local para ligar ao gateway de rede virtual Azure.
5. Descarregue o perfil ponto-a-local a partir do portal Azure e distribua aos clientes

Para aprender a configurar um túnel VPN local,, consulte [esta ligação.](./tutorial-site-to-site-portal.md)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>FAQ para autenticação de certificado azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>FAQ para autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Configurar uma ligação P2S - Autenticação Azure AD](openvpn-azure-ad-tenant.md)

* [Configure uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - Autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**