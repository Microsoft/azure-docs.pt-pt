---
title: Proteger os recursos da sua rede no Azure Security Center
description: Este documento aborda recomendações no Azure Security Center que o ajudam a proteger os recursos da rede Azure e a manter-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 28d0d7b14c18a1d98be1872b1555930b2e1650c3
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342455"
---
# <a name="protect-your-network-resources"></a>Proteja os seus recursos de rede
O Azure Security Center analisa continuamente o estado de segurança dos seus recursos Azure para as melhores práticas de segurança da rede. Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o orientam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

Para obter uma lista completa das recomendações relativas ao networking, consulte [as recomendações de networking](recommendations-reference.md#recs-network).

Este artigo aborda recomendações que se aplicam aos seus recursos Azure numa perspetiva de segurança de rede. As recomendações de networking centram-se em torno de firewalls de próxima geração, Grupos de Segurança de Rede, acesso JIT VM, regras de tráfego de entrada excessivamente permissivas, e muito mais. Para obter uma lista de recomendações de networking e ações de reparação, consulte [recomendações de segurança de gestão no Centro de Segurança Azure](security-center-recommendations.md).

As funcionalidades de **Networking** do Centro de Segurança incluem: 

- Mapa de rede (requer Azure Defender)
- [Endurecimento adaptativo da rede](security-center-adaptive-network-hardening.md) (requer Azure Defender)
- Recomendações de segurança em rede
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Veja os seus recursos de networking e as suas recomendações

A partir da página de inventário de [ativos,](asset-inventory.md)utilize o filtro de tipo de recurso para selecionar os recursos de rede que pretende investigar:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Tipos de recursos de rede de inventário de ativos" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Mapa de rede

O mapa de rede interativa fornece uma visão gráfica com sobreposições de segurança, dando-lhe recomendações e insights para endurecer os recursos da sua rede. Utilizando o mapa, pode ver a topologia da rede das suas cargas de trabalho Azure, as ligações entre as suas máquinas virtuais e sub-redes, e a capacidade de perfurar do mapa em recursos específicos e as recomendações para esses recursos.

Para abrir o mapa da Rede:

1. A partir do menu do Security Center, abra o painel de instrumentos do Azure Defender e selecione **o mapa de Rede**.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Tipos de recursos de rede de inventário de ativos" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Selecione o menu **Layers** escolha **Topologia.**
 
A vista padrão do mapa de topologia apresenta:

- Subscrições selecionadas em Azure. O mapa suporta várias subscrições.
- VMs, sub-redes e VNets do tipo de recurso Resource Manager (os recursos clássicos do Azure não são suportados)
- VNets espreitados
- Apenas recursos que tenham [recomendações de rede](security-center-recommendations.md) com uma severidade alta ou média  
- Internet enfrentando recursos
- O mapa está otimizado para as subscrições selecionadas no Azure. Se modificar a sua seleção, o mapa é recalculado e reotimizado com base nas suas novas definições.  

[![Mapa de topologia em rede](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Compreender o mapa da rede

O mapa de rede pode mostrar-lhe os seus recursos Azure numa vista **de Topologia** e numa vista **de tráfego.** 

### <a name="the-topology-view"></a>A vista topologia

Na vista **topologia** do mapa de networking, pode ver as seguintes informações sobre os seus recursos de networking:

- No círculo interno, pode ver todos os Vnets dentro das suas subscrições selecionadas, o próximo círculo são todas as sub-redes, o círculo exterior são todas as máquinas virtuais.
- As linhas que ligam os recursos no mapa permitem saber quais os recursos associados entre si e como a sua rede Azure está estruturada. 
- Use os indicadores de gravidade para obter rapidamente uma visão geral dos quais os recursos têm recomendações abertas do Centro de Segurança.
- Pode clicar em qualquer um dos recursos para os aprofundar e ver os detalhes desse recurso e as suas recomendações diretamente, e no contexto do mapa da Rede.  
- Se há demasiados recursos a serem exibidos no mapa, o Azure Security Center usa o seu algoritmo proprietário para agrupar os seus recursos inteligentes, destacando os recursos que estão no estado mais crítico, e tem as recomendações de maior gravidade. 

Como o mapa é interativo e dinâmico, cada nó é clicável, e a vista pode mudar com base nos filtros:

1. Pode modificar o que vê no mapa da rede utilizando os filtros na parte superior. Pode concentrar o mapa com base em:

   -  **Saúde de segurança**: Pode filtrar o mapa com base na Severidade (Alta, Média, Baixa) dos seus recursos Azure.
   - **Recomendações**: Pode selecionar quais os recursos apresentados com base nas recomendações que estão ativas nesses recursos. Por exemplo, só pode visualizar recursos para os quais o Security Center recomenda que ative os Grupos de Segurança da Rede.
   - **Zonas de rede**: Por padrão, o mapa apresenta apenas recursos virados para a Internet, pode selecionar VMs internos também.
 
2. Pode clicar em **Reset** no canto superior esquerdo a qualquer momento para devolver o mapa ao seu estado padrão.

Para aprofundar um recurso:

1. Quando seleciona um recurso específico no mapa, o painel direito abre-se e dá-lhe informações gerais sobre o recurso, soluções de segurança conectadas, se houver, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que seleciona. 
2. Quando paira sobre um nó no mapa, pode ver informações gerais sobre o recurso, incluindo subscrição, tipo de recurso e grupo de recursos.
3. Utilize o link para ampliar a ponta da ferramenta e concentre o mapa nesse nó específico. 
4. Para reorientar o mapa para longe de um nó específico, faça um zoom para fora.

### <a name="the-traffic-view"></a>A vista de tráfego

A vista **de tráfego** fornece-lhe um mapa de todo o tráfego possível entre os seus recursos. Isto fornece-lhe um mapa visual de todas as regras que configuraste que definem quais os recursos que podem comunicar com quem. Isto permite-lhe ver a configuração existente dos grupos de segurança da rede, bem como identificar rapidamente possíveis configurações de risco dentro das suas cargas de trabalho.

### <a name="uncover-unwanted-connections"></a>Descubra ligações indesejadas

A força desta visão está na sua capacidade de lhe mostrar estas ligações permitidas juntamente com as vulnerabilidades que existem, para que possa utilizar esta secção transversal de dados para realizar o endurecimento necessário nos seus recursos. 

Por exemplo, pode detetar duas máquinas que não sabia que podiam comunicar, permitindo-lhe isolar melhor as cargas de trabalho e as sub-redes.

### <a name="investigate-resources"></a>Investigar recursos

Para aprofundar um recurso:

1. Quando seleciona um recurso específico no mapa, o painel direito abre-se e dá-lhe informações gerais sobre o recurso, soluções de segurança conectadas, se houver, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que seleciona. 
2. Clique em **Tráfego** para ver a lista de possíveis tráfego de saída e entrada no recurso - esta é uma lista completa de quem pode comunicar com o recurso e com quem pode comunicar, e através dos quais protocolos e portas. Por exemplo, quando seleciona um VM, todos os VMs com os quais pode comunicar são mostrados, e quando seleciona uma sub-rede, todas as sub-redes com as quais pode comunicar são mostradas.

**Estes dados baseiam-se na análise dos Grupos de Segurança da Rede, bem como em algoritmos avançados de aprendizagem automática que analisam múltiplas regras para compreender os seus cruzamentos e interações.** 

[![Mapa de tráfego de rede](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações aplicáveis a outros tipos de recursos Azure, consulte o seguinte:

- [Proteger as máquinas e aplicações no Centro de Segurança do Azure](./asset-inventory.md)