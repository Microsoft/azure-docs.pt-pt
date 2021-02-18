---
title: Insights para O Balançador de Carga Azure
description: Utilize os insights do equilibrador de carga para obter uma rápida localização de falhas e decisões de design informadas
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 190a9e431dedfb0f0fe6077e903174f253f5c773
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589107"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Utilização de Insights para monitorizar e configurar o seu Balanceador de Carga Azure

Através do Azure Monitor para redes, é-lhe fornecidas visualizações de dependência funcional e painel de métricas pré-configurados para os seus Balanceadores de Carga. Estes visuais ajudam a capacitar-se para tomar decisões de design informadas e rapidamente localizar, diagnosticar e resolver quaisquer falhas.

>[!NOTE] 
>Por favor, note que esta funcionalidade está na Pré-visualização e a visão de dependência funcional e o painel de instrumentos pré-configurado podem mudar para melhorar esta experiência

>[!IMPORTANT]
>O Balanceador de Carga Padrão é necessário para ver métricas do espaço de nome do balanceador de carga no painel de métricas pré-configurado. Ainda será possível ver métricas a partir do conjunto de vm, escala de máquina virtual e espaços de nome do Monitor de Ligação, no entanto, recomendamos [o upgrade para a Standard](./upgrade-basic-standard.md) para qualquer carga de trabalho de produção para tirar partido do conjunto robusto de métricas do Balancer de Carga.

## <a name="functional-dependency-view"></a>Vista de dependência funcional

A visão de dependência funcional permitir-lhe-á imaginar até as configurações mais complexas do balançador de carga. Com feedback visual na sua última configuração do Balancer de Carga, pode fazer atualizações mantendo a sua configuração em mente.

Pode aceder a esta vista visitando a lâmina insights do seu recurso Balancer de Carga em Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecição da visão de dependência funcional. A parte frontal do balançador de carga pode ser vista conectando-se aos membros do pool backend através das regras configuradas. Para o Balanceador de Carga Padrão, as linhas desde as regras de equilíbrio de carga até às instâncias de backend pool são codificadas por cores com base no estado da sonda de saúde." border="true":::

Para os Balanceadores de Carga Padrão, os recursos do seu pool backend são codificados por cores com o estado da Sonda de Saúde indicando a disponibilidade atual do seu pool de backend para servir o tráfego. Paralelamente à topologia acima é-lhe apresentado um gráfico de estado de saúde em termos de tempo, dando uma visão instantânea da saúde da sua aplicação.

## <a name="metrics-dashboard"></a>Dashboard de métricas

A partir da lâmina insights do seu Balancer de Carga, pode selecionar métricas mais detalhadas para ver um livro de trabalho pré-configurado [do Monitor Azure](../azure-monitor/visualize/workbooks-overview.md) contendo métricas métricas relevantes para aspetos específicos do seu Balanceador de Carga. Este painel mostrará o estado do Balanceador de Carga e ligará à documentação relevante no topo da página.

No início, será apresentado com o separador Visão Geral. Pode navegar através dos separadores disponíveis cada um dos quais contém visuais relevantes para um aspeto específico do seu Balancer de Carga. A orientação explícita para cada um está disponível no painel de instrumentos na parte inferior de cada separador.

Os separadores do painel de instrumentos atualmente disponíveis são:
* Descrição Geral
* Disponibilidade de Front-end e Back-end
* Débito de Dados
* Distribuição de Fluxos
* Monitores de Ligação
* Definições de Métricas 

### <a name="overview-tab"></a>Separador de visão geral
O separador 'Vista Geral' contém uma grelha pes pes pesalhável com o estado geral da disponibilidade do caminho dos dados e do estado da sonda de saúde para cada um dos IPs frontend ligados ao seu Balanceador de Carga. Estas métricas indicam se o FRONTend IP é responsivo e as instâncias de computação no seu Backend Pool são individualmente responsivas às ligações de entrada.

Também pode ver o resultado geral de dados para cada FRONTend IP nesta página para ter uma ideia de se está a produzir e receber níveis de tráfego esperados. A orientação na parte inferior da página irá direcioná-lo para o separador apropriado caso veja quaisquer valores irregulares.

### <a name="frontend-and-backend-availability-tab"></a>Separador de disponibilidade frontend e backend
Os separadores frontend e backend Availability mostram as métricas de produção de caminhos de dados e de estado da sonda de saúde apresentadas em algumas visões úteis. O primeiro gráfico mostra o valor agregado para que possa determinar se há algum problema. O resto dos gráficos mostram estas métricas divididas por várias dimensões para que possa resolver problemas e identificar as fontes de quaisquer problemas de disponibilidade de entrada.

Um fluxo de trabalho para visualizar estes gráficos é fornecido na parte inferior da página com causas comuns para vários sintomas. 

### <a name="data-throughput-tab"></a>Separador de dados
O separador Data Throughput permite-lhe rever a sua entrada e saída para identificar se os seus padrões de tráfego são como esperado. Mostrará a entrada e saída de dados divididos pela Frontend IP e frontend Port para que possa identificar se os serviços que tem em execução estão a funcionar individualmente.

### <a name="flow-distribution"></a>Distribuição de Fluxos
O Separador de Distribuição de Fluxo irá ajudá-lo a visualizar e gerir o número de fluxos que as suas instâncias de backend estão a receber e a produzir. Mostra a Taxa de Criação de Fluxo e a Contagem de Fluxos para o tráfego de entrada e saída, bem como o Tráfego de Rede que cada VM e a escala virtual de escala definida estão recebendo. 

Estas opiniões podem dar-lhe feedback sobre se a configuração do Balancer de Carga ou os padrões de tráfego estão a levar a um tráfego desequilibrado. Por exemplo, se tiver a afinidade da sessão configurada e um único cliente estiver a fazer um número desproporcionado de pedidos. Também lhe informará se estiver a aproximar-se do [limite de fluxo por VM](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) para o tamanho da sua máquina.

### <a name="connection-monitors"></a>Monitores de Ligação
O separador Monitores de Ligação mostrar-lhe-á a latência de ida e volta num mapa global para todos os Monitores de [Conexão](../network-watcher/connection-monitor.md)  que configuraste. Estes visuais fornecem informações úteis para serviços com requisitos rigorosos de latência. Para satisfazer os seus requisitos poderá ser necessário adicionar implementações regionais adicionais ou passar para um modelo [de equilíbrio de carga regional transversal](./cross-region-overview.md)

### <a name="metric-definitions"></a>Definições de Métricas
O separador Definições Métricas contém todas as informações mostradas no [artigo métricas multidimensionais](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).

## <a name="next-steps"></a>Passos seguintes
* Reveja o painel de instrumentos e forneça feedback usando o link abaixo se houver algo que possa ser melhorado
* [Reveja a documentação métrica para garantir que compreende como cada métrica é calculada](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Criar monitores de ligação para o seu balanceador de carga](../network-watcher/connection-monitor.md)
* [Crie os seus próprios livros](../azure-monitor/visualize/workbooks-overview.md)de trabalho, pode inspirar-se clicando no botão de edição no seu painel de métricas detalhado
