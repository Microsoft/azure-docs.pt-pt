---
title: 'Ligue-se a um VNet utilizando P2S VPN & vários tipos de autenticação: portal'
titleSuffix: Azure VPN Gateway
description: Ligue-se a um VNet via P2S utilizando vários tipos de autenticação no portal Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746708"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Configure uma ligação VPN ponto-a-local a um VNet utilizando vários tipos de autenticação: Portal Azure

Este artigo ajuda-o a ligar de forma segura clientes individuais que executam Windows, Linux ou macOS a um Azure VNet. As ligações VPN Ponto a Site são úteis quando pretende ligar à VNet a partir de uma localização remota, como em teletrabalho em casa ou numa conferência. Pode também utilizar P2S em vez de uma VPN Site a Site, quando são poucos os clientes que precisam de ligar a uma VNet. As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou de IKEv2. Para obter mais informações sobre VPN Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Ligue de um computador a um Azure VNet - diagrama de ligação ponto-a-local":::

Para obter mais informações sobre a VPN ponto-a-local, consulte [Sobre a VPN ponto a local.](point-to-site-about.md) Para criar esta configuração utilizando o Azure PowerShell, consulte [configurar uma VPN ponto a local utilizando a Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

Vários tipos de autenticação no mesmo gateway VPN são suportados apenas com o tipo de túnel OpenVPN.

### <a name="example-values"></a><a name="example"></a>Valores de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

* **Nome da VNet:** VNet1
* **Espaço do endereço:** 10.1.0.0/16<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 10.1.0.0/24
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos:** TestRG1
* **Localização:** Leste dos EUA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nome do gateway de rede virtual:** VNet1GW
* **Tipo de gateway:** VPN
* **Tipo de VPN:** baseado na rota
* **Nome do endereço IP público:** VNet1GWpip
* **Tipo de ligação:** Ponto a site
* **Piscina de endereços do cliente:** 172.16.201.0/24<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente especificado.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Criar uma rede virtual

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Gateway de rede virtual

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

>[!NOTE]
>O Gateway Basic SKU não suporta o tipo de túnel OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Conjunto de endereços de cliente

O conjunto de endereços de cliente é um conjunto de endereços IP privados que especificar. Os clientes que se ligam através da VPN Ponto a Site recebem dinamicamente um endereço IP deste intervalo. Utilize um intervalo de endereços IP privados que não se sobreponha à localização no local a partir da qual se ligou ou à VNet à qual se quer ligar. Se configurar vários protocolos e o SSTP for um dos protocolos, então o conjunto de endereços configurado é dividido entre os protocolos configurados de forma igual.

1. Assim que o gateway da rede virtual for criado, navegue até à secção **Definições** da página do gateway de rede virtual. Em **Definições**, selecione **configuração ponto-a-local**. Selecione **Configurar agora** para abrir a página de configuração.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Screenshot da página de configuração ponto-a-local." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Na página **de configuração ponto-a-local,** pode configurar uma variedade de configurações. Na caixa de **piscina Address,** adicione o intervalo de endereço IP privado que pretende utilizar. Os clientes VPN recebem dinamicamente um endereço IP do intervalo que especificou. A máscara mínima de sub-rede é de 29 bits para configuração ativa/passiva e 28 bits para configuração ativa/ativa.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Screenshot da piscina de endereços.":::

1. Continue até ao troço seguinte para configurar os tipos de autenticação e túnel.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Tipos de autenticação e túneis

Nesta secção, configura-se o tipo de autenticação e o tipo de túnel. Na página **de configuração ponto-a-local,** se não vir **o tipo de túnel** ou o tipo de **autenticação,** o seu gateway está a utilizar o SKU Básico. A SKU Básica não suporta a autenticação IKEv2 ou RADIUS. Se pretender utilizar estas definições, tem de eliminar e recriar o gateway utilizando um gateway diferente SKU.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Screenshot do tipo de autenticação.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tipo de túnel

Na página **de configuração ponto-a-local,** selecione **OpenVPN (SSL)** como o tipo de túnel.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Tipo de autenticação

Para **o tipo de autenticação,** selecione os tipos pretendidos. As opções são:

* Certificado Azure
* RADIUS
* Azure Active Directory

Dependendo do tipo de autenticação selecionado, verá diferentes campos de definição de configuração que terão de ser preenchidos. Preencha as informações necessárias e **selecione Guarde** no topo da página para guardar todas as definições de configuração.

Para obter mais informações sobre o tipo de autenticação, consulte:

* [Certificado Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Pacote de configuração do cliente VPN

Os clientes VPN devem ser configurados com as definições de configuração do cliente. O pacote de configuração do cliente VPN contém ficheiros com as definições para configurar clientes VPN de forma a ligar-se a um VNet sobre uma ligação P2S.

Para obter instruções para gerar e instalar ficheiros de configuração do cliente VPN, utilize o artigo relativo à sua configuração:

* [Crie e instale ficheiros de configuração do cliente VPN para configurações P2S de autenticação de certificados Azure nativos](point-to-site-vpn-client-configuration-azure-cert.md).
* [Autenticação do Azure Ative Directory: Configurar um cliente VPN para ligações de protocolo P2S OpenVPN](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>FAQ ponto-a-local

Esta secção contém informações sobre faq que dizem respeito às configurações ponto-a-local. Também pode ver as FAQ de [Gateway VPN](vpn-gateway-vpn-faq.md) para obter informações adicionais sobre o Gateway VPN.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](../index.yml). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/network-overview.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
