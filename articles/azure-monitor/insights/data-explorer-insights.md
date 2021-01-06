---
title: Monitor Azure Data Explorer Cluster com Monitor Azure para Azure Data Explorer (pré-visualização)/ Microsoft Docs
description: Este artigo descreve Azure Monitor Insights para Azure Data Explorer Clusters.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: e5884cbbc13fb2353898c617dffdf18b114f1b86
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901797"
---
# <a name="monitoring-your-azure-data-explorer-cluster-with-azure-monitor-for-azure-data-explorer-preview"></a>Monitorização do seu Cluster Azure Data Explorer com monitor Azure para Azure Data Explorer (pré-visualização)
O Azure Monitor for Azure Data Explorer (pré-visualização) proporciona uma monitorização abrangente dos seus clusters, proporcionando uma visão unificada do desempenho do cluster, operações, utilização e falhas.
Este artigo irá ajudá-lo a entender como embarcar e utilizar o Azure Monitor para O Azure Data Explorer (pré-visualização).

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Introdução ao Monitor Azure para Azure Data Explorer (pré-visualização)

Antes de entrar na experiência, deve entender como apresenta e visualiza a informação.
-    **Na perspetiva da escala** mostrando uma visão instantânea das métricas primárias dos seus clusters, para rastrear facilmente o desempenho de consultas, ingestão e operações de exportação.
-   **Faça a análise** de um cluster Azure Data Explorer para ajudar a realizar análises detalhadas.
-    **Personalizável** onde pode alterar quais as métricas que deseja ver, modificar ou definir limiares que se alinham com os seus limites e guardar os seus próprios livros de trabalho personalizados. Os gráficos do livro podem ser fixados aos dashboards Azure.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Vista do Monitor Azure (na perspetiva da escala)

A partir do Azure Monitor, você pode ver as principais métricas de desempenho para o cluster, incluindo métricas para consultas, ingestão e operações de exportação de vários clusters na sua subscrição, e ajudar a identificar problemas de desempenho.

Para ver o desempenho dos seus clusters em todas as suas subscrições, execute os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/)

2. Selecione **Monitor** a partir do painel da esquerda no portal Azure, e sob a secção Insights Hub, selecione **Azure Data Explorer Clusters (pré-visualização)**.

![Screenshot da experiência de visão geral com vários gráficos](./media/data-explorer-insights/insights-hub.png)

### <a name="overview-tab"></a>Separador de visão geral

No **separador Visão Geral** para a subscrição selecionada, a tabela apresenta métricas interativas para os clusters Azure Data Explorer agrupados dentro da subscrição. Pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de entrega:

* Subscrições – apenas subscrições que tenham clusters Azure Data Explorer estão listadas.

* Aglomerados Azure Data Explorer – por padrão, apenas até cinco clusters são pré-selecionados. Se selecionar todos ou vários clusters no seletor de âmbito, serão devolvidos até 200 clusters.

* Intervalo de tempo – por padrão, apresenta as últimas 24 horas de informação com base nas respetivas seleções es feitas.

O contador, na lista de drop-down, aumenta o número total de clusters Azure Data Explorer nas subscrições selecionadas e reflete quantos são selecionados. Existem códigos de cores condicionais para as colunas: Manter vivo, CPU, Utilização de Ingestão e Utilização de Cache. As células codificadas por laranja têm valores que não são sustentáveis para o cluster. 

Para melhor entender o que cada uma das métricas representa, recomendamos a leitura através da documentação sobre as [métricas do Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Separador de desempenho de consulta

Este separador mostra a duração da consulta, o número total de consultas simultâneas e o número total de consultas estranguladas.

![Screenshot do separador de desempenho de consulta](./media/data-explorer-insights/query-performance.png)

### <a name="ingestion-performance-tab"></a>Separador de desempenho de ingestão

Este separador mostra a latência da ingestão, os resultados de ingestão bem sucedidos, os resultados falhados da ingestão, o volume de ingestão e os eventos processados para os Hubs de Evento/IoT.

[![Screenshot do separador de desempenho da ingestão](./media/data-explorer-insights/ingestion-performance.png)](./media/data-explorer-insights/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Separador streaming Ingest Performance

Este separador fornece informações sobre a taxa média de dados, duração média e taxa de pedido.

### <a name="export-performance-tab"></a>Separador de desempenho de exportação

Este separador fornece informações sobre registos exportados, atrasos, contagem pendente e percentagem de utilização para operações de exportação contínuas.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Vista a partir de um recurso Azure Data Explorer Cluster (análise de perfuração)

Para aceder ao Azure Monitor para Azure Data Explorer Clusters diretamente de um Cluster Azure Data Explorer:

1. No portal Azure, selecione **Azure Data Explorer Clusters**.

2. Na lista, escolha um Agrupamento de Exploradores de Dados Azure. Na secção de monitorização, escolha **Insights (pré-visualização)**.

Estas vistas também são acessíveis selecionando o nome de recurso de um cluster Azure Data Explorer a partir da vista de insights do Azure Monitor.

O Azure Monitor for Azure Data Explorer combina registos e métricas para fornecer uma solução de monitorização global. A inclusão de visualizações baseadas em registos requer que os utilizadores [permitam o registo de diagnóstico do seu cluster Azure Data Explorer e envie-os para um espaço de trabalho log Analytics.](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) Os registos de diagnóstico que devem ser ativados são: **Comando,** **Consulta,** **TableDetails** e **TableUsageStatistics**.

![Screenshot do botão azul que exibe o texto "Ativar registos para monitorização"](./media/data-explorer-insights/enable-logs.png)


 O **separador visão** geral mostra:

- Telhas métricas que destacam a disponibilidade e o estado geral do cluster para avaliar rapidamente a sua saúde.

- Um resumo das [recomendações ativas](https://docs.microsoft.com/azure/data-explorer/azure-advisor) do Advisor e do estado [de saúde dos recursos.](https://docs.microsoft.com/azure/data-explorer/monitor-with-resource-health)

- Gráficos que mostram os melhores consumidores de CPU e memória e o número de utilizadores únicos ao longo do tempo.


[![Screenshot de vista de um recurso de cluster Azure Data Explorer](./media/data-explorer-insights/overview.png)](./media/data-explorer-insights/overview.png#lightbox)

O separador **Métricas-Chave** mostra uma visão unificada de algumas das métricas do cluster, agrupadas por: métricas gerais, relacionadas com consultas, relacionadas com a ingestão e métricas relacionadas com a ingestão de streaming.

[![Screenshot da vista de falhas](./media/data-explorer-insights/key-metrics.png)](./media/data-explorer-insights/key-metrics.png#lightbox)

O separador **Utilização** permite que os utilizadores mergulhem profundamente no desempenho dos comandos e consultas do cluster. Nesta página, pode:
 
 - Veja quais os utilizadores e aplicações que estão a enviar mais consultas ou a consumir mais CPU e memória (para que possa entender quais os utilizadores que estão a enviar as consultas mais pesadas para o cluster processar).
 - Identifique os principais utilizadores e aplicações por consultas falhadas.
 - Identificar alterações recentes no número de consultas, em comparação com a média diária histórica (nos últimos 16 dias), por utilizador e aplicação.
 - Identificar tendências e picos no número de consultas, memória e consumo de CPU por utilizador, aplicação e tipo de comando.

[![Screenshot da visualização de operações com gráficos de donuts de aplicação superior por contagem de comando e consulta, principais de topo por contagem de comandos e consultas, e comandos de topo por tipos de comando](./media/data-explorer-insights/usage.png)](./media/data-explorer-insights/usage.png#lightbox)

[![Screenshot de visão de operações com gráficos de linha de contagem de consultas por aplicação, memória total por aplicação e CPU total por aplicação](./media/data-explorer-insights/usage-2.png)](./media/data-explorer-insights/usage-2.png#lightbox)

O separador **tabelas** mostra as mais recentes e históricas propriedades das tabelas no cluster. Você pode ver quais as tabelas que estão consumindo mais espaço, rastrear a história de crescimento pelo tamanho da mesa, dados quentes, e o número de linhas ao longo do tempo.

O separador **cache** permite que os utilizadores analisem os padrões de retrospetiva das suas consultas reais e os comparem com a política de cache configurada (para cada tabela). Pode identificar tabelas usadas pela maioria das consultas e tabelas que não são questionadas e adaptar a política de cache em conformidade. Pode obter recomendações de política de cache específicas em tabelas específicas no Azure Advisor (atualmente, as recomendações de cache estão disponíveis apenas a partir do painel principal do [Azure Advisor),](https://docs.microsoft.com/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)com base no olhar real das consultas nos últimos 30 dias e uma política de cache não otimizada para pelo menos 95% das consultas. Recomendações de redução de cache no Azure Advisor estão disponíveis para clusters que são "limitados por dados" (o que significa que o cluster tem baixa CPU e baixa utilização de ingestão, mas devido à alta capacidade de dados, o cluster não conseguiu escalar ou reduzir a escala). 

[![Screenshot dos detalhes da cache](./media/data-explorer-insights/cache-tab.png)](./media/data-explorer-insights/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Pin to Azure dashboard

Pode fixar qualquer uma das secções métricas (da perspetiva "à escala") a um painel Azure selecionando o ícone de pino de pressão no canto superior direito da secção.

![Screenshot do ícone pin selecionado](./media/data-explorer-insights/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Personalize o Monitor Azure para o Azure Data Explorer Cluster

Esta secção destaca cenários comuns para a edição do livro para personalizar em apoio às necessidades de análise de dados:
* Scope o livro para selecionar sempre uma subscrição específica ou um Cluster de Exploradores de Dados Azure
* Alterar métricas na grelha
* Alterar limiares ou renderização/codificação de cores

Pode iniciar personalizações ativando o modo de edição, selecionando o botão **Personalizar** a partir da barra de ferramentas superior.

![Screenshot do botão de personalização](./media/data-explorer-insights/customize.png)

As personalizações são guardadas num livro personalizado para evitar a sobreescrita da configuração padrão no nosso livro publicado. Os livros de trabalho são guardados dentro de um grupo de recursos, quer na secção My Reports que é privada para si, quer na secção Relatórios Partilhados que é acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, tem de ir à galeria do livro para o lançar.

![Screenshot da galeria de livros](./media/data-explorer-insights/gallery.png)

## <a name="troubleshooting"></a>Resolução de problemas

Para obter orientações gerais sobre a resolução de problemas, consulte o artigo dedicado de resolução de [problemas](troubleshoot-workbooks.md)baseado em livros de trabalho .

Esta secção irá ajudá-lo com o diagnóstico e resolução de problemas de algumas das questões comuns que poderá encontrar ao utilizar o Azure Monitor para O Azure Data Explorer Cluster (pré-visualização). Utilize a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Por que não vejo todas as minhas assinaturas no selecionador de assinaturas

Apenas mostramos subscrições que contenham Clusters Azure Data Explorer, escolhidos a partir do filtro de subscrição selecionado, que são selecionados no "Diretório + Subscrição" no cabeçalho do portal Azure.

![Screenshot do filtro de subscrição](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Por que não vejo nenhum dado para o meu Cluster Azure Data Explorer sob as secções de Utilização, Tabelas ou Cache

Para visualizar os dados baseados em registos, terá de [ativar registos](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) de diagnóstico para cada um dos Clusters Azure Data Explorer que pretende monitorizar. Isto pode ser feito sob as definições de diagnóstico de cada cluster. Terá de enviar os seus dados para um espaço de trabalho do Log Analytics. Os registos de diagnóstico que devem ser ativados são: Comando, Consulta, TableDetails e TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Já ativei registos para o meu Azure Data Explorer Cluster, porque é que ainda não consigo ver os meus dados sob Comandos e Consultas

Atualmente, os registos de diagnóstico não funcionam retroativamente, pelo que os dados só começarão a aparecer depois de terem sido tomadas ações para o seu Azure Data Explorer. Portanto, pode levar algum tempo, variando de horas a um dia, dependendo do quão ativo é o seu cluster Azure Data Explorer.


## <a name="next-steps"></a>Passos seguintes

Saiba os cenários que os livros de trabalho são projetados para apoiar, como autorizar novos e personalizar relatórios existentes, e mais através da revisão [criar relatórios interativos com os livros de trabalho do Azure Monitor.](../platform/workbooks-overview.md)
