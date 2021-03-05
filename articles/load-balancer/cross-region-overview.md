---
title: Balanceador de carga transversal (pré-visualização)
titleSuffix: Azure Load Balancer
description: Visão geral do nível do balançador de carga da região transversal para o Balançador de Carga Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: b0e51d11797bc5767f19b25a92a2d29a66ea1bb2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176727"
---
# <a name="cross-region-load-balancer-preview"></a>Balanceador de carga transversal (Pré-visualização)

O Azure Load Balancer distribui o tráfego de entrada que chega ao frontend do balançador de carga para anular as instâncias da piscina.

O Azure Standard Load Balancer suporta o equilíbrio da carga entre regiões, permitindo cenários de HA geo-redundantes, tais como:

* Tráfego de entrada proveniente de várias regiões.
* [Falha global instantânea](#regional-redundancy) para a próxima implantação regional ideal.
* Distribuição de carga em regiões até à região de Azure mais próxima com [latência ultra-baixa.](#ultra-low-latency)
* Capacidade de escalar para [cima/para baixo](#ability-to-scale-updown-behind-a-single-endpoint) atrás de um único ponto final.
* [IP estático](#static-ip)
* [Preservação ip do cliente](#client-ip-preservation)
* [Baseie-se na](#build-cross-region-solution-on-existing-azure-load-balancer) solução existente do balanceador de carga sem curva de aprendizagem

> [!IMPORTANT]
> O equilibrador de carga transversal está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O equilíbrio entre regiões de carga oferece os mesmos benefícios de alto desempenho e baixa latência que o balanceador regional de carga padrão. 

A configuração IP frontal do seu balanceador de carga cross-region é estática e publicitada na maioria das [regiões Azure.](#participating-regions)

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagrama do equilibrador de carga entre regiões." border="true":::

> [!NOTE]
> A porta de apoio da sua regra de equilíbrio de carga no balançador de carga transversal deve coincidir com a porta frontal da regra de equilíbrio de carga/regra nat de entrada no equilibrador de carga padrão regional. 

### <a name="regional-redundancy"></a>Redundância regional

Configure o despedimento regional adicionando um endereço IP público global aos seus equilibradores de carga existentes. 

Se uma região falhar, o tráfego é encaminhado para o próximo e mais próximo e saudável equilibrador regional de carga.  

A sonda de saúde do equilibrador de carga cross-region reúne informações sobre a disponibilidade a cada 20 segundos. Se um equilibrador regional de carga baixar a sua disponibilidade para 0, o equilibrador de carga transversal detetará a falha. O balançador regional de carga é então retirado da rotação. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagrama da visão de tráfego da região global." border="true":::

### <a name="ultra-low-latency"></a>Latência ultra-baixa

O algoritmo de equilíbrio de carga de geo-proximidade baseia-se na localização geográfica dos seus utilizadores e nas suas implementações regionais. 

O tráfego iniciado a partir de um cliente atingirá a região mais próxima e viajará através da espinha dorsal da rede global da Microsoft para chegar à implementação regional mais próxima. 

Por exemplo, tem um equilibrador de carga transversal com equilibradores de carga padrão nas regiões de Azure:

* E.U.A. Oeste
* Europa do Norte

Se um fluxo é iniciado a partir de Seattle, o tráfego entra nos EUA. Esta região é a região mais próxima de Seattle. O tráfego é encaminhado para o balanceador de carga da região mais próximo, que é o Oeste dos EUA.

O equilibrador de carga transversal Azure utiliza algoritmo de equilíbrio de carga de geo-proximidade para a decisão de encaminhamento. 

O modo de distribuição de carga configurado dos equilibradores regionais de carga é utilizado para tomar a decisão final de encaminhamento quando vários equilibradores de carga regionais são utilizados para a geo-proximidade.

Para obter mais informações, consulte [configurar o modo de distribuição para O Balancer de Carga Azure](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Capacidade de escalar para cima/para baixo atrás de um único ponto final

Quando expõe o ponto final global de um equilibrador de carga entre regiões aos clientes, pode adicionar ou remover as implementações regionais atrás do ponto final global sem interrupção. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>IP estático
O balanceador de carga transversal vem com um IP público estático, que garante que o endereço IP permanece o mesmo. Para saber mais sobre IP estático, leia mais [aqui](../virtual-network/public-ip-addresses.md#allocation-method)

### <a name="client-ip-preservation"></a>Preservação IP do cliente
O equilibrador de carga transversal é um equilibrador de carga de rede de passagem de camadas 4. Esta passagem preserva o IP original do pacote.  O IP original está disponível para o código em execução na máquina virtual. Esta preservação permite-lhe aplicar lógica específica a um endereço IP.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Construa a solução de região transversal no equilibrador de carga Azure existente
O pool de backend do balanceador de carga transversal contém um ou mais equilibradores de carga regionais. 

Adicione as suas implementações existentes do balanceador de carga a um equilibrador de carga entre regiões para uma implantação transnacentes altamente disponível.

**A região natal** é onde o equilibrador de carga transversal é implantado. Esta região não afeta a forma como o tráfego será encaminhado. Se uma região natal diminuir, o fluxo de tráfego não é afetado.

### <a name="home-regions"></a>Regiões de origem
* E.U.A. Leste 2
* E.U.A. Oeste
* Europa Ocidental
* Sudeste Asiático
* E.U.A. Central
* Europa do Norte
* Ásia Leste

> [!NOTE]
> Só pode implantar o seu equilibrador de carga transversal numa das 8 regiões acima.

Uma **região participante** é onde está disponível o IP público global do equilibrador de carga. 

O tráfego iniciado pelo utilizador viajará para a região mais próxima através da rede central da Microsoft. 

O balançador de carga transversal encaminha o tráfego para o equilibrador regional de carga adequado.

### <a name="participating-regions"></a>Regiões participantes
* E.U.A. Leste 
* Europa Ocidental 
* E.U.A. Central 
* E.U.A. Leste 2 
* E.U.A. Oeste 
* Europa do Norte 
* E.U.A. Centro-Sul 
* E.U.A. Oeste 2 
* Sul do Reino Unido 
* Sudeste Asiático 
* E.U.A. Centro-Norte 
* Leste do Japão 
* Ásia Leste 
* E.U.A. Centro-Oeste 
* Austrália Sudeste 
* Leste da Austrália 
* Índia Central 

## <a name="limitations"></a>Limitações

* As configurações IP frontend cross-region são apenas públicas. Atualmente, não é suportado um frontend interno.

* O equilibrador de carga privado ou interno não pode ser adicionado ao pool de backend de um equilibrador de carga transversal 

* As configurações IP frontend IPv6 de região cruzada não são suportadas. 

* Uma sonda de saúde não pode ser configurada atualmente. Uma sonda de saúde predefinida recolhe automaticamente informações de disponibilidade sobre o balançador regional de carga a cada 20 segundos. 

* A integração com o Serviço Azure Kubernetes (AKS) não está disponível atualmente. A perda de conectividade ocorrerá ao implantar um equilibrador de carga transversal com o equilibrador de carga Standard com o cluster AKS implantado no backend.

## <a name="pricing-and-sla"></a>Preços e SLA
Balanceador de carga transversal, partilha o [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) do balanceador de carga padrão.

 
## <a name="next-steps"></a>Passos seguintes

- Ver [Tutorial: Criar um equilibrador de carga transversal utilizando o portal Azure](tutorial-cross-region-portal.md) para criar um equilibrador de carga transversal.
- Consulte [Criar um equilibrador de carga padrão público](quickstart-load-balancer-standard-public-portal.md) para criar um balanceador regional de carga padrão.
- Saiba mais sobre [o Azure Load Balancer](load-balancer-overview.md).
