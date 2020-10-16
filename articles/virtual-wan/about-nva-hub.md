---
title: 'Azure Virtual WAN: Sobre o aparelho virtual de rede no centro'
description: Neste artigo, você vai aprender sobre aparelhos virtuais de rede no hub VIRTUAL WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 1e4b8a2d801d7d7eccfaf558c3926ead1ab0a953
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313778"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Sobre o aparelho virtual de rede num hub Azure Virtual WAN (Pré-visualização)

A Azure Virtual WAN tem trabalhado com parceiros de networking para construir automatização que facilita a ligação dos seus Equipamentos de Instalação do Cliente (CPE) a uma porta de entrada Azure VPN no centro virtual. O Azure está a trabalhar com parceiros de rede selecionados para permitir que os clientes implementem um aparelho virtual de rede de terceiros (NVA) diretamente no centro virtual. Isto permite aos clientes que pretendam ligar o seu CPE à mesma marca NVA no centro virtual para que possam tirar partido das capacidades proprietárias da SD-WAN.

A Barracuda Networks é o primeiro parceiro a fornecer uma oferta NVA que pode ser implantada diretamente no hub Virtual WAN com o seu produto [Barracuda CloudGen WAN.](https://www.barracuda.com/products/cloudgenwan) O Azure está a trabalhar com mais parceiros, por isso espera ver outras ofertas a seguir.

> [!NOTE]
> Apenas as ofertas da NVA que estão disponíveis para serem implantadas no hub Virtual WAN podem ser implantadas no centro VIRTUAL WAN. Não podem ser implantados numa rede virtual arbitrária em Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Como funciona?

Os NVAs que estão disponíveis para serem implantados diretamente no hub Azure Virtual WAN são projetados especificamente para serem usados no centro virtual. A oferta da NVA é publicada no Azure Marketplace como uma Aplicação Gerida, e os clientes podem implementar a oferta diretamente a partir do Azure Marketplace, ou podem implementar a oferta a partir do hub virtual através do portal Azure.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Visão geral do processo":::

A oferta de NVA de cada parceiro terá uma experiência e funcionalidade ligeiramente diferentes com base nos seus requisitos de implementação. No entanto, existem algumas coisas que são comuns em todas as ofertas de parceiros para NVA no centro Virtual WAN.

* Uma experiência de aplicação gerida oferecida através do Azure Marketplace.
* Capacidade e faturação da Unidade de Infraestrutura da NVA.
* As métricas de saúde surgiram através do Monitor Azure.

### <a name="managed-application"></a><a name="managed"></a>Aplicação gerida

Todas as ofertas da NVA que estão disponíveis para serem implantadas no hub virtual WAN terão uma **Aplicação Gerida** que está disponível no Azure Marketplace. As Aplicações Geridas permitem aos parceiros fazer o seguinte:

* Construa uma experiência de implementação personalizada para o seu NVA.
* Forneça um modelo especializado de Gestor de Recursos que lhes permita criar o NVA diretamente no hub VIRTUAL WAN.
* Custos de licenciamento de software de bill diretamente, ou através do Azure Marketplace.
* Expor propriedades personalizadas e contadores de recursos.

A NVA Partners pode criar diferentes recursos dependendo da sua implementação do aparelho, licenciamento de configuração e necessidades de gestão. Quando um cliente cria um NVA no hub Virtual WAN, como todas as Aplicações Geridas, haverá dois Grupos de Recursos criados na sua subscrição.

* **Grupo de Recursos do Cliente** - Isto conterá um espaço reservado para a aplicação gerida. Os parceiros podem usá-lo para expor quaisquer propriedades do cliente que escolherem aqui.
* **Grupo de Recursos Geridos** - Os clientes não podem configurar ou alterar recursos diretamente neste grupo de recursos, uma vez que este é controlado pelo editor da Aplicação Gerida. Este Grupo de Recursos conterá o recurso **NetworkVirtualAppliances.**

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Visão geral do processo":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Unidades de Infraestruturas NVA

Quando criar um NVA no hub Virtual WAN, deve escolher o número de Unidades de Infraestrutura NVA com as quais pretende implantá-lo. Uma **Unidade de Infraestrutura NVA** é uma unidade de capacidade de largura de banda agregada para um NVA no centro Virtual WAN. Uma **Unidade de Infraestrutura NVA** é semelhante a uma Unidade de [Escala](pricing-concepts.md#scale-unit) VPN em termos da forma como pensa sobre capacidade e dimensionamento.

* 1 Unidade de Infraestrutura NVA representa 500 Mbps de largura de banda agregada para todas as ligações do local da sucursal que entram neste NVA, com um custo de $0,25/hora.
* O Azure suporta 1-80 Unidades de Infraestrutura NVA para uma determinada implantação de hub virtual NVA.
* Cada parceiro pode oferecer diferentes pacotes da Unidade de Infraestrutura NVA que são um subconjunto de todas as configurações suportadas da Unidade de Infraestruturas NVA.

Semelhante às Unidades de Escala VPN, se escolher *1 Unidade de Infraestrutura NVA = 500 Mbps,* implica que serão criados dois casos de redundância, cada um com um rendimento máximo de 500 Mbps. Por exemplo, se tivesse cinco ramos, cada um fazendo 10 Mbps no ramo, precisaria de um agregado de 50 Mbps na extremidade da cabeça. O planeamento da capacidade agregada do NVA deve ser efetuado após a avaliação da capacidade necessária para apoiar o número de balcões no centro.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Processo de configuração do aparelho virtual de rede

Os parceiros trabalharam para proporcionar uma experiência que configura a NVA automaticamente como parte do processo de implementação. Uma vez que o NVA tenha sido abastado no centro virtual, qualquer configuração adicional que possa ser necessária para o NVA deve ser feita através do portal de parceiros NVA ou aplicação de gestão. O acesso direto à NVA não está disponível.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Recursos de site e conexão com NVAs

Ao contrário das configurações do Gateway Azure VPN, não precisa de criar recursos **do Site,** recursos **de conexão Site-a-Site** ou recursos **de conexão ponto-a-local** para ligar os seus sites de filial ao seu NVA no centro Virtual WAN. Tudo isto é gerido através do parceiro NVA.

Ainda precisa de criar ligações Hub-to-VNet para ligar o seu hub VIRTUAL WAN às suas redes virtuais Azure.

## <a name="supported-regions"></a><a name="regions"></a>Regiões suportadas

A NVA no centro virtual está disponível para visualização nas seguintes regiões:

|Região geopolítica | Regiões do Azure|
|---|---|
| América do Norte| West US, South Central US, East US 2   |
| América do Sul | Sul do Brasil |
| Europa | West Europe, Reino Unido Sul|
|  Médio Oriente | Uae Norte |
| Ásia | Leste do Japão |
| Austrália | Leste da Austrália |

## <a name="faq"></a>FAQ

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Sou um parceiro de eletrodomésticos de rede e quero colocar a nossa NVA no centro.  Posso juntar-me a este programa de parceiros?

Infelizmente, não temos capacidade para embarcar quaisquer novos parceiros neste momento. Volte a verificar connosco em novembro!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Posso colocar qualquer NVA do Azure Marketplace no centro virtual WAN?

N.º Neste momento, apenas [o Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer) está disponível para ser implantado no centro virtual WAN.

### <a name="what-is-the-cost-of-the-nva"></a>Qual é o custo da NVA?

Tem de adquirir uma licença para o seu Barracuda CloudGen WAN NVA da Barracuda. Para obter mais informações sobre o licenciamento, consulte [a página Wan CloudGen da Barracuda.](https://www.barracuda.com/products/cloudgenwan) Além disso, também irá incorrer em encargos da Microsoft para as Unidades de Infraestrutura NVA que consome, bem como quaisquer outros recursos que utilize. Para mais informações, consulte [os Conceitos de Preços.](pricing-concepts.md)

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Posso colocar um NVA num centro básico?

N.º Tem de utilizar um hub Standard se quiser implantar um NVA.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Posso colocar um NVA num centro seguro?

Sim. Barracuda CloudGen WAN pode ser implantado num hub com Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Posso ligar algum dispositivo CPE na minha sucursal à Barracuda CloudGen WAN NVA no centro?

N.º Barracuda CloudGen WAN só é compatível com dispositivos Barracuda CPE. Para saber mais sobre os requisitos da CloudGen WAN, consulte [a página Wan CloudGen da Barracuda.](https://www.barracuda.com/products/cloudgenwan)

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Que cenários de encaminhamento são suportados com a NVA no centro?

Todos os cenários de encaminhamento suportados por VIRTUAL WAN são suportados com NVAs no centro.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o VIRTUAL WAN, consulte o artigo Geral da [WAN Virtual.](virtual-wan-about.md)