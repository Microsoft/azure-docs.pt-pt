---
title: Encaminhe o tráfego através de NVAs usando configurações personalizadas
titleSuffix: Azure Virtual WAN
description: Este cenário ajuda-o a passar o tráfego através de NVAs utilizando um NVA diferente para tráfego com ligação à Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e51d7d00120f6facb0fb53a8e379d157ae79ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938565"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Cenário: Encaminhar o tráfego através de NVAs utilizando configurações personalizadas

Quando está a trabalhar com o encaminhamento virtual wan virtual do Azure, tem várias opções disponíveis para si. O foco deste artigo é quando se pretende encaminhar o tráfego através de um aparelho virtual de rede (NVA) para comunicação entre redes virtuais e ramos, e utilizar um NVA diferente para tráfego ligado à Internet. Para obter mais informações, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a>Design

* **Porta-vozes** de redes virtuais ligadas ao centro virtual. (Por exemplo, VNet 1, VNet 2 e VNet 3 no diagrama mais tarde neste artigo.)
* **Serviço VNet** para redes virtuais onde os utilizadores implementaram um NVA para inspecionar o tráfego não internet, e possivelmente com serviços comuns acedidos por raios de estado. (Por exemplo, VNet 4 no diagrama mais tarde neste artigo.) 
* **Perímetro VNet** para redes virtuais onde os utilizadores implementaram um NVA para ser usado para inspecionar o tráfego ligado à Internet. (Por exemplo, VNet 5 no diagrama mais tarde neste artigo.)
* **Hubs** para centros DE WAN virtuais geridos pela Microsoft.

O quadro seguinte resume as ligações suportadas neste cenário:

| De          | Para|Spokes|Serviço VNet|Ramos|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes**| ->| diretamente |diretamente | através do Serviço VNet |através do Perímetro VNet |
| **Serviço VNet**| ->| diretamente |n/a| diretamente | |
| **Ramos** | ->| através do Serviço VNet |diretamente| diretamente |  |

Cada uma das células da matriz de conectividade descreve se a conectividade flui diretamente sobre o WAN Virtual ou sobre uma das redes virtuais com um NVA. 

Repare nos seguintes detalhes:
* Porta-vozes:
  * Os porta-vozes chegarão a outros porta-vozes diretamente sobre os centros virtuais de WAN.
  * Os raios-raios obterão conectividade com os ramos através de uma rota estática que aponta para o VNet de serviço. Não aprendem prefixos específicos dos ramos, porque são mais específicos e substituem o resumo.
  * Os porta-vozes enviarão tráfego de internet para o Perímetro VNet através de um olhar vnet direto.
* Os ramos chegarão aos raios através de um encaminhamento estático que aponta para o VNet de serviço. Não aprendem prefixos específicos das redes virtuais que sobrepõem a rota estática resumida.
* O VNet de Serviço será semelhante a um VNet de Serviços Partilhados que precisa de ser acessível a partir de todas as redes virtuais e de todas as sucursais.
* O Perimeter VNet não precisa de ter conectividade sobre o WAN Virtual, porque o único tráfego que irá suportar vem sobre os olhos diretos da rede virtual. No entanto, para simplificar a configuração, utilize o mesmo modelo de conectividade que o Do Perímetro VNet.

Existem três padrões distintos de conectividade, que se traduz em três tabelas de rotas. As associações às diferentes redes virtuais são:

* Porta-vozes:
  * Tabela de rotas associada: **RT_V2B**
  * Propagação para tabelas de rotas: **RT_V2B** e **RT_SHARED**
* VNets NVA (Service VNet e DMZ VNet):
  * Tabela de rotas associada: **RT_SHARED**
  * Propagação para tabelas de rotas: **RT_SHARED**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **RT_SHARED** e **Padrão**

> [!NOTE] 
> Certifique-se de que os VNets falados não se propagam à etiqueta Predefinido. Isto garante que o tráfego de sucursais para VNets falados será encaminhado para as NVAs.

Estas rotas estáticas asseguram que o tráfego de e para a rede virtual e sucursal passa pela NVA no VNet de serviço (VNet 4):

| Descrição | Tabela de rota | Rota estática              |
| ----------- | ----------- | ------------------------- |
| Ramos    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| NVA fala  | Predefinição     | 10.1.0.0/16 -> vnet4conn  |

Agora pode utilizar o VIRTUAL WAN para selecionar a ligação correta para enviar os pacotes para. Também precisa de usar o WAN Virtual para selecionar as medidas corretas a tomar ao receber esses pacotes. Utilize as tabelas de rota de ligação para isto, da seguinte forma:

| Descrição | Ligação | Rota estática            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

Para obter mais informações, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a>Arquitetura

Aqui está um diagrama da arquitetura descrito anteriormente no artigo.

Há um centro, chamado **Hub 1.**

* **O Hub 1** está diretamente ligado aos VNet **4** e **VNet 5** da NVA VNet .

* Espera-se que o tráfego entre as VNets 1, 2 e 3 e os balcões se despassem através do **VNet 4 NVA** 10.4.0.5.

* Espera-se que todo o tráfego ligado à Internet a partir de VNets 1, 2 e 3 vá através do **VNet 5 NVA** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Diagrama de arquitetura de rede.":::

## <a name="workflow"></a>Fluxo de trabalho

Para configurar o encaminhamento via NVA, aqui estão os passos a considerar:

1. Para o tráfego ligado à Internet passar pelo VNet 5, precisa de VNets 1, 2 e 3 para ligar diretamente através da rede virtual que espreita o VNet 5. Também precisa de uma rota definida pelo utilizador configurada nas redes virtuais para 0.0.0.0/0 e o próximo salto 10.5.0.5. Atualmente, o Virtual WAN não permite um próximo hop NVA no centro virtual para 0.0.0.0/0.

1. No portal Azure, vá ao seu centro virtual e crie uma tabela de rota personalizada chamada **RT_Shared**. Esta tabela aprende rotas através da propagação de todas as redes virtuais e conexões de ramificação. Pode ver esta mesa vazia no diagrama seguinte.

   * **Rotas:** Não precisa de adicionar rotas estáticas.

   * **Associação:** Selecione VNets 4 e 5, o que significa que as ligações destas redes virtuais associam-se à tabela de rotas **RT_Shared**.

   * **Propagação:** Porque pretende que todos os balcões e ligações de rede virtuais propaguem as suas rotas dinamicamente para esta tabela de rotas, selecione filiais e todas as redes virtuais.

1. Crie uma tabela de rotas personalizada chamada **RT_V2B** para direcionar o tráfego dos VNets 1, 2 e 3 para os balcões.

   * **Rotas:** Adicione uma entrada de rota estática agregada para os ramos, com o próximo lúpulo como a ligação VNet 4. Configure uma rota estática na ligação do VNet 4 para prefixos de ramificação, e indique que o próximo lúpulo é o IP específico do NVA no VNet 4.

   * **Associação:** Selecione todos os VNets 1, 2 e 3. Isto implica que as ligações VNet 1, 2 e 3 associarão a esta tabela de rotas e serão capazes de aprender rotas (estáticas e dinâmicas através da propagação) nesta tabela de rotas.

   * **Propagação:** As ligações propagam rotas para as tabelas de rotas. Selecionar VNets 1, 2 e 3 permitem rotas de propagação de VNets 1, 2 e 3 para esta tabela de rotas. Não há necessidade de propagar rotas das ligações de filiais para **RT_V2B**, porque o tráfego de rede virtual de ramificação passa pela NVA no VNet 4.
  
1. Editar a tabela de rotas predefinido, **DefaultRouteTable**.

   Todas as ligações VPN, Azure ExpressRoute e VPN do utilizador estão associadas à tabela de rotas predefinida. Todas as ligações VPN, ExpressRoute e VPN do utilizador propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Rotas:** Adicione uma entrada estática agregada para VNets 1, 2 e 3, com o próximo salto como a ligação VNet 4. Configure uma rota estática na ligação do VNet 4 para os prefixos agregados VNet 1, 2 e 3, e indicar o próximo lúpulo ser o IP específico do NVA no VNet 4.

   * **Associação:** Certifique-se de que a opção para os balcões (VPN/ER/P2S) está selecionada, garantindo que as ligações de filial no local estão associadas à tabela de rotas predefinida.

   * **Propagação a partir de:** Certifique-se de que a opção para os balcões (VPN/ER/P2S) é selecionada, certificando-se de que as ligações no local estão a propagar rotas para a tabela de rotas predefinido.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Diagrama de fluxo de trabalho." lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
