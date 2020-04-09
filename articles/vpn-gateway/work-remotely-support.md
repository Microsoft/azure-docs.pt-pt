---
title: 'Trabalho remoto com P2S: Azure VPN Gateway'
description: Esta página descreve como pode alavancar o VPN Gateway para permitir o trabalho remotamente devido à pandemia COVID-19.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 2d07a13c654f30e48c37d2e8d3e801166e26f4f4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886590"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Trabalho remoto utilizando Azure VPN Gateway Ponto-a-local

>[!NOTE]
>Este artigo descreve como pode alavancar o Azure VPN Gateway, o Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

Este artigo descreve as opções disponíveis para as organizações para estabelecer acesso remoto aos seus utilizadores ou complementar as suas soluções existentes com capacidade adicional durante a epidemia COVID-19.

A solução azure ponto-a-local é baseada na nuvem e pode ser aprovisionada rapidamente para atender ao aumento da procura de utilizadores para trabalhar a partir de casa. Pode escalar facilmente e desligar-se com a mesma facilidade e rapidez quando a capacidade aumentada já não é necessária.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Sobre vpn ponto-a-local

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para os telecommuters que pretendam ligar-se a VNets Azure ou centros de dados no local a partir de um local remoto, como em casa ou numa conferência. Este artigo descreve como permitir que os utilizadores trabalhem remotamente com base em vários cenários.

A tabela abaixo mostra os sistemas operativos do cliente e as opções de autenticação que lhes estão disponíveis. Seria útil selecionar o método de autenticação baseado no OS do cliente que já está em uso. Por exemplo, selecione OpenVPN com autenticação baseada em Certificado se tiver uma mistura de sistemas operativos do cliente que precisam de se ligar. Além disso, por favor, note que a VPN ponto-a-site só é suportada em gateways VPN baseados em rotas.

![ponto-a-local](./media/working-remotely-support/ostable.png "SO")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Cenário 1 - Os utilizadores precisam de acesso a recursos apenas no Azure

Neste cenário, os utilizadores remotos apenas precisam de aceder aos recursos que se encontram no Azure.

![ponto-a-local](./media/working-remotely-support/scenario1.png "Cenário 1")

A um nível elevado, são necessários os seguintes passos para permitir aos utilizadores a ligação aos recursos Do Azure de forma segura:

1. Crie um portal de rede virtual (se não existir).
2. Configure VPN ponto-a-local no portal.

   * Para autenticação de certificado, siga [este link](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Para o OpenVPN, siga [este link](vpn-gateway-howto-openvpn.md).
   * Para autenticação Azure AD, siga [este link](openvpn-azure-ad-tenant.md).
   * Para obter ligações ponto-a-local de resolução de problemas, siga [este link](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Descarregue e distribua a configuração do cliente VPN.
4. Distribua os certificados (se for selecionada a autenticação do certificado) aos clientes.
5. Ligue-se à VPN Azure.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Cenário 2 - Os utilizadores precisam de acesso a recursos em recursos Azure e/ou on-prem

Neste cenário, os utilizadores remotos precisam de aceder aos recursos que se encontram no Azure e no centro de dados no local.

![ponto-a-local](./media/working-remotely-support/scenario2.png "Cenário 2")

A um nível elevado, são necessários os seguintes passos para permitir aos utilizadores a ligação aos recursos Do Azure de forma segura:

1. Crie um portal de rede virtual (se não existir).
2. Configure VPN ponto-a-local no gateway (ver [Cenário 1](#scenario1)).
3. Configure um túnel local-a-local no portal da rede virtual Azure com BGP ativado.
4. Configure o dispositivo no local para ligar ao portal de rede virtual Azure.
5. Descarregue o perfil ponto-a-site do portal Azure e distribua aos clientes

Para aprender a configurar um túnel VPN local-a-local, consulte [esta ligação](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>FAQ para autenticação de certificado nativo Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>FAQ para autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Configure uma ligação P2S - Autenticação Azure AD](openvpn-azure-ad-tenant.md)

* [Configurar uma ligação P2S - autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma ligação P2S - autenticação de certificado nativo Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registada da OpenVPN Inc.**