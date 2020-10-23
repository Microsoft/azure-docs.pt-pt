---
title: 'Cenário: Encaminhar o tráfego através de NVAs usando configurações personalizadas'
titleSuffix: Azure Virtual WAN
description: Este cenário ajuda-o a passar o tráfego através de NVAs usando um NVA diferente para tráfego ligado à Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 031cbb48a7e0c572866dc591d26fb1e6b6b12dba
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424718"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Cenário: Tráfego de rotas através de NVAs - personalizado (Pré-visualização)

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário NVA (Network Virtual Appliance), o objetivo é encaminhar o tráfego através de um NVA para comunicação entre VNets e ramos, e usar um NVA diferente para tráfego ligado à Internet. Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Neste cenário, utilizaremos a convenção de nomeação:

* "Raios" para redes virtuais ligadas ao centro virtual (VNet 1, VNet 2 e VNet 3 na **Figura 1**).
* "Service VNet" para redes virtuais onde os utilizadores tenham implantado um NVA (VNet 4 na **Figura 1**) para inspecionar o tráfego não internet e, possivelmente, com serviços comuns acedidos por raios de voz.
* "DMZ VNet" para redes virtuais onde os utilizadores tenham implantado um NVA para inspecionar o tráfego ligado à Internet (VNet 5 na **Figura 1**).
* "Hubs" para os Centros VIRTUAIS WAN geridos pela Microsoft.

A seguinte matriz de conectividade resume os fluxos suportados neste cenário:

**Matriz de conectividade**

| De          | Para:|*Spokes*|*Serviço VNet*|*Ramos*|*Internet*|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes**| &#8594;| Diretamente |Diretamente | Através do Serviço VNet |Através da DMZ VNet |
| **Serviço VNet**| &#8594;| Diretamente |n/a| Diretamente | |
| **Ramos** | &#8594;| Através do Serviço VNet |Diretamente| Diretamente |  |

Cada uma das células da matriz de conectividade descreve se a conectividade flui diretamente sobre o WAN Virtual ou sobre um dos VNets com um NVA. Vamos em detalhe sobre as diferentes linhas:

* Porta-vozes:
  * Os porta-vozes chegarão a outros porta-vozes diretamente sobre os centros virtuais de WAN.
  * Os raios-raios obterão conectividade com os ramos através de uma rota estática que aponta para o VNet de serviço. Não devem aprender prefixos específicos dos ramos (caso contrário, estes seriam mais específicos e anulariam o resumo).
  * Os porta-vozes enviarão tráfego de Internet para o DMZ VNet através de um olhar vnet direto.
* Ramos:
  * Os ramos chegarão aos raios através de um encaminhamento estático que aponta para o VNet de serviço. Não devem aprender prefixos específicos dos VNets que substituem a rota estática resumida.
* O VNet de serviço será semelhante a um VNet de Serviços Partilhados que precisa de ser acessível a partir de cada VNet e de cada ramo.
* O DMZ VNet não precisa realmente de ter conectividade sobre o WAN Virtual, uma vez que o único tráfego que irá suportar virá sobre os seus pares VNet diretos. No entanto, usaremos o mesmo modelo de conectividade que o DMZ VNet para simplificar a configuração.

A nossa matriz de conectividade dá-nos três padrões distintos de conectividade, que se traduz em três tabelas de rotas. As associações às diferentes VNets serão as seguintes:

* Porta-vozes:
  * Tabela de rotas associada: **RT_V2B**
  * Propagação para tabelas de rotas: **RT_V2B** e **RT_SHARED**
* VNets NVA (Service VNet e DMZ VNet):
  * Tabela de rotas associada: **RT_SHARED**
  * Propagação para tabelas de rotas: **RT_SHARED**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **RT_SHARED** e **Padrão**

Precisamos destas rotas estáticas para garantir que o tráfego VNet-to-branch e branch-to-VNet passe pela NVA no VNet de serviço (VNet 4):

| Descrição | Tabela de rota | Rota estática              |
| ----------- | ----------- | ------------------------- |
| Ramos    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| Porta-vozes da NVA  | Predefinição     | 10.1.0.0/16 -> vnet4conn  |

Agora o VIRTUAL WAN sabe para que ligação enviar os pacotes, mas a ligação precisa de saber o que fazer ao receber esses pacotes: É aqui que são utilizadas as tabelas de rota de ligação.

| Descrição | Ligação | Rota estática            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

Neste momento, tudo deve estar no lugar.

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Na **Figura 1,** há um hub, **Hub 1**.

* **O Hub 1** está diretamente ligado aos VNet **4** e **VNet 5**da NVA VNet .

* Espera-se que o tráfego entre as VNets 1, 2 e 3 e os Ramos (VPN/ER/P2S) seja realizado através do **VNet 4 NVA** 10.4.0.5.

* Espera-se que todo o tráfego ligado à Internet a partir de VNets 1, 2 e 3 vá através do **VNet 5 NVA** 10.5.0.5.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figura 1":::

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para configurar o encaminhamento via NVA, aqui estão os passos a considerar:

1. Para que o tráfego ligado à Internet seja realizado através do VNet 5, precisa de VNets 1, 2 e 3 para ligar diretamente via VNet olhando para vNet 5. Também precisa de um UDR configurado nos VNets para 0.0.0.0/0 e o próximo salto 10.5.0.5. Atualmente, o Virtual WAN não permite um próximo LNVA de lúpulo no centro virtual para 0.0.0.0/0.

1. No portal Azure, navegue até ao seu hub virtual e crie uma tabela de rotas personalizada **RT_Shared** que aprenderá rotas através da propagação de todas as ligações VNets e Branch. Na **Figura 2**, esta é representada como uma tabela de rota personalizada vazia **RT_Shared**.

   * **Rotas:** Não é necessário adicionar rotas estáticas.

   * **Associação:** Selecione VNets 4 e 5, o que significa que as ligações VNets 4 e 5 associam-se à tabela de rotas **RT_Shared**.

   * **Propagação:** Uma vez que pretende que todos os ramos e ligações VNet propaguem as suas rotas dinamicamente para esta tabela de rotas, selecione os balcões e todos os VNets.

1. Crie uma tabela de rotas personalizada **RT_V2B** para direcionar o tráfego dos VNets 1, 2 e 3 para os balcões.

   * **Rotas:** Adicione uma entrada estática agregada para ramos (VPN/ER/P2S) (10.2.0.0/16 na **Figura 2**) com o próximo salto como ligação VNet 4. Também é necessário configurar uma rota estática na ligação do VNet 4 para prefixos de ramificação, e indicar que o próximo lúpulo será o IP específico do NVA no VNet 4.

   * **Associação:** Selecione todos os VNets 1, 2 e 3. Isto implica que as ligações VNet 1, 2 e 3 associarão a esta tabela de rotas e serão capazes de aprender rotas (estáticas e dinâmicas através da propagação) nesta tabela de rotas.

   * **Propagação:** As ligações propagam rotas para as tabelas de rotas. A seleção dos VNets 1, 2 e 3 permitirá a propagação das rotas de propagação dos VNets 1, 2 e 3 para esta tabela de rotas. Não há necessidade de propagar rotas das ligações de filiais para RT_V2B, uma vez que o tráfego de ramal VNet passa pela NVA no VNet 4.
  
1. Editar a tabela de rotas padrão **DefaultRouteTable**.

   Todas as ligações VPN, ExpressRoute e User VPN estão associadas à tabela de rotas predefinida. Todas as ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Rotas:** Adicione uma entrada estática agregada para vNets 1, 2 e 3 (10.1.0.0/16 na **Figura 2**) com o próximo salto como ligação VNet 4. Também precisa configurar uma rota estática na ligação do VNet 4 para prefixos agregados VNet 1, 2 e 3, e indicar que o próximo salto será o IP específico do NVA no VNet 4.

   * **Associação:** Certifique-se de que a opção para os balcões (VPN/ER/P2S) está selecionada, garantindo que as ligações de sucursais no local estão associadas ao *padrão rotautetable*.

   * **Propagação a partir de:** Certifique-se de que a opção para os balcões (VPN/ER/P2S) está selecionada, garantindo que as ligações no local são rotas de propagação para o *padrão rotatável*.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figura 1" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
