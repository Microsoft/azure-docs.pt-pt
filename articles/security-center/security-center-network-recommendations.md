---
title: Proteger os seus recursos de rede no Azure Security Center
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
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431490"
---
# <a name="protect-your-network-resources"></a>Proteja os recursos da sua rede
O Azure Security Center analisa continuamente o estado de segurança dos seus recursos Azure para as melhores práticas de segurança da rede. Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

Este artigo explica a página **de Networking** da secção de segurança de recursos do Centro de Segurança.

Para obter uma lista completa das recomendações para o Networking, consulte as recomendações de [Networking.](recommendations-reference.md#recs-network)

Este artigo aborda recomendações que se aplicam aos seus recursos Azure numa perspetiva de segurança da rede. As recomendações de networking centram-se em torno de firewalls de próxima geração, Grupos de Segurança de Rede, acesso JIT VM, regras de tráfego excessivamente permissivas, e muito mais. Para obter uma lista de recomendações de networking e ações de reparação, consulte a Gestão de recomendações de segurança no Centro de [Segurança Azure.](security-center-recommendations.md)

> [!NOTE]
> A página **Networking** permite mergulhar profundamente na sua saúde de recursos Azure a partir de uma perspetiva de rede. O mapa da rede e os controlos de rede adaptativo estão disponíveis apenas para o nível padrão do Azure Security Center. [Se utilizar o nível gratuito, pode clicar no botão para ver o **legacy networking** e receber recomendações](#legacy-networking)de recursos de rede .
>

A página **Networking** fornece uma visão geral das secções em que pode mergulhar profundamente, para obter mais informações sobre a saúde dos recursos da sua rede:

- Mapa de rede (apenas nível padrão do Centro de Segurança Azure)
- Proteção de Rede Ajustável
- Recomendações de segurança em rede.
- Lâmina de **rede** legacy (a lâmina de rede anterior) 
 
[painel de networking ![](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Mapa da rede
O mapa de rede interativo fornece uma visão gráfica com sobreposições de segurança que lhe dão recomendações e insights para endurecer os recursos da sua rede. Utilizando o mapa, pode ver a topologia da rede das suas cargas de trabalho Azure, as ligações entre as suas máquinas virtuais e subredes, e a capacidade de aprofundar do mapa em recursos específicos e as recomendações para esses recursos.

Para abrir o mapa da Rede:

1. No Centro de Segurança, sob higiene de segurança de recursos, selecione **Networking**.
2. Em mapa **de rede** clique Em **ver topologia**.
 
A visão padrão do mapa de topologia mostra:

- Assinaturas selecionadas em Azure. O mapa suporta várias subscrições.
- VMs, subredes e VNets do tipo de recursos do Gestor de Recursos (os recursos Clássicos Do Azure não são suportados)
- Peered VNets
- Apenas recursos que têm [recomendações](security-center-recommendations.md) de rede com uma severidade elevada ou média  
- Recursos virados para a Internet
- O mapa está otimizado para as subscrições selecionadas no Azure. Se modificar a sua seleção, o mapa é recalculado e reotimizado com base nas suas novas definições.  

[mapa de topologia de rede ![](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Compreender o mapa da Rede

O mapa da Rede pode mostrar-lhe os seus recursos Azure numa vista **topologia** e uma vista de **tráfego.** 

### <a name="the-topology-view"></a>A vista de topologia

Na visão **topologia** do mapa de networking, pode ver as seguintes informações sobre os seus recursos de networking:

- No círculo interno, pode ver todos os Vnets dentro das suas subscrições selecionadas, o próximo círculo são todas as subredes, o círculo exterior são todas as máquinas virtuais.
- As linhas que ligam os recursos no mapa permitem-lhe saber quais os recursos associados uns aos outros e como a sua rede Azure está estruturada. 
- Utilize os indicadores de gravidade para obter rapidamente uma visão geral dos recursos que têm recomendações abertas do Centro de Segurança.
- Pode clicar em qualquer um dos recursos para os aprofundar e ver os detalhes desse recurso e as suas recomendações diretamente, e no contexto do mapa da Rede.  
- Se há demasiados recursos a serem exibidos no mapa, o Azure Security Center usa o seu algoritmo proprietário para agrupar os seus recursos, destacando os recursos que estão no estado mais crítico, e têm as recomendações de maior gravidade. 

Como o mapa é interativo e dinâmico, cada nó é clicável, e a vista pode mudar com base nos filtros:

1. Pode modificar o que vê no mapa da rede utilizando os filtros na parte superior. Pode concentrar o mapa com base em:

   -  **Saúde de segurança:** Pode filtrar o mapa com base na Severidade (Alta, Média, Baixa) dos seus recursos Azure.
   - **Recomendações**: Pode selecionar quais os recursos apresentados com base em quais as recomendações que estão ativas nesses recursos. Por exemplo, só pode visualizar recursos para os quais o Security Center recomenda que ative grupos de segurança de rede.
   - **Zonas**de rede : Por padrão, o mapa exibe apenas recursos virados para a Internet, pode selecionar também VMs internos.
 
2. Pode clicar em **Reset** no canto superior esquerdo a qualquer momento para devolver o mapa ao seu estado padrão.

Para perfurar um recurso:

1. Quando seleciona um recurso específico no mapa, o painel certo abre-se e dá-lhe informações gerais sobre o recurso, soluções de segurança conectadas se houver algum, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que seleciona. 
2. Quando paira sobre um nó no mapa, pode ver informações gerais sobre o recurso, incluindo subscrição, tipo de recurso e grupo de recursos.
3. Utilize o link para ampliar a ponta da ferramenta e refoque o mapa nesse nó específico. 
4. Para reorientar o mapa para longe de um nó específico, faça zoom para fora.

### <a name="the-traffic-view"></a>A vista de tráfego

A vista **de tráfego** fornece-lhe um mapa de todo o tráfego possível entre os seus recursos. Isto fornece-lhe um mapa visual de todas as regras configuradas que definem quais os recursos que podem comunicar com quem. Isto permite-lhe ver a configuração existente dos grupos de segurança da rede, bem como identificar rapidamente possíveis configurações de risco dentro das suas cargas de trabalho.

### <a name="uncover-unwanted-connections"></a>Descubra ligações indesejadas

A força desta visão está na sua capacidade de mostrar-lhe estas conexões permitidas juntamente com as vulnerabilidades que existem, para que possa utilizar esta secção transversal de dados para realizar o endurecimento necessário dos seus recursos. 

Por exemplo, pode detetar duas máquinas que não sabia poderem comunicar, permitindo-lhe isolar melhor as cargas de trabalho e as subredes.

### <a name="investigate-resources"></a>Investigar recursos

Para perfurar um recurso:

1. Quando seleciona um recurso específico no mapa, o painel certo abre-se e dá-lhe informações gerais sobre o recurso, soluções de segurança conectadas se houver algum, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que seleciona. 
2. Clique no **Tráfego** para ver a lista de possíveis tráfegos de saída e de entrada no recurso - esta é uma lista completa de quem pode comunicar com o recurso e com quem pode comunicar, e através dos quais protocolos e portas. Por exemplo, quando seleciona um VM, todos os VMs com os quais pode comunicar são mostrados, e quando seleciona uma sub-rede, todas as subredes com as quais pode comunicar são mostradas.

**Estes dados baseiam-se na análise dos Grupos de Segurança da Rede, bem como em algoritmos avançados de aprendizagem automática que analisam várias regras para compreender os seus crossovers e interações.** 

[mapa de tráfego em rede ![](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## Rede legacy<a name ="legacy-networking"></a>

Se não tiver o nível Padrão do Centro de Segurança, esta secção explica como visualizar recomendações gratuitas de Networking.

Para aceder a esta informação, na lâmina de networking, clique em **Ver o legado em rede**. 

[![Legacy Networking](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Secção pontos finais com acesso à Internet
Na secção **de pontos finais virados para** a Internet, pode ver as máquinas virtuais que estão atualmente configuradas com um ponto final virado para a Internet e o seu estado.

Esta tabela tem o nome final, o endereço IP virado para a Internet e o estado atual de gravidade do grupo de segurança da rede e as recomendações da NGFW. A mesa é ordenada pela gravidade.

### <a name="networking-topology-section"></a>Secção Topologia de redes
A secção de topologia de **Networking** tem uma visão hierárquica dos recursos.

Esta tabela está classificada (máquinas virtuais e subnets) por gravidade.

Nesta vista de topologia, o primeiro nível exibe Vnets. A segunda apresenta subredes, e o terceiro nível exibe as máquinas virtuais que pertencem a essas subredes. A coluna correta mostra o estado atual das recomendações do grupo de segurança da rede para esses recursos.

O terceiro nível apresenta máquinas virtuais, que são semelhantes às descritas anteriormente. Pode clicar em qualquer recurso para saber mais ou aplicar o controlo ou configuração de segurança necessários.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger o seu serviço Azure SQL no Azure Security Center](security-center-sql-service-recommendations.md)