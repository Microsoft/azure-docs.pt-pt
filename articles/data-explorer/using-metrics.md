---
title: Monitor Azure Data Explorer desempenho, saúde e uso com métricas
description: Aprenda a usar as métricas do Azure Data Explorer para monitorizar o desempenho, saúde e utilização do cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: f8078d8bae00ac4789a679be4d7a1944c749cce6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423849"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitor Azure Data Explorer desempenho, saúde e uso com métricas

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Azure Data Explorer, cria-se primeiro um cluster e cria-se uma ou mais bases de dados nesse cluster. Em seguida, ingere (carregar) dados numa base de dados para que possa fazer perguntas contra ele. As métricas do Azure Data Explorer fornecem indicadores-chave sobre a saúde e desempenho dos recursos do cluster. Utilize as métricas detalhadas neste artigo para monitorizar a saúde e desempenho do cluster Azure Data Explorer no seu cenário específico como métricas autónomas. Também pode utilizar métricas como base para os [painéis azure](/azure/azure-portal/azure-portal-dashboards) operacionais e [alertas Azure.](/azure/azure-monitor/platform/alerts-metric-overview)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, pode criar uma [conta Azure gratuita.](https://azure.microsoft.com/free/)
* Um [cluster e base de dados.](create-cluster-database-portal.md)

## <a name="using-metrics"></a>Usando métricas

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. No seu cluster Azure Data Explorer, selecione **Métricas** para abrir o painel de métricas e começar a analisar o seu cluster.
    ![Selecione métricas](media/using-metrics/select-metrics.png).
1. No painel de métricas: painel de métricas ![](media/using-metrics/metrics-pane.png)
    1. Para criar um gráfico métrico, selecione o nome **métrico** e a **agregação** relevante por métrica. Os pick-ups **De Recurso** e **Nomes Métricos** são pré-selecionados para o seu cluster Azure Data Explorer. Para obter mais informações sobre métricas diferentes, consulte [as métricas suportadas do Azure Data Explorer.](#supported-azure-data-explorer-metrics)
    1. **Selecione Adicionar métrica** para ver várias métricas traçadas no mesmo gráfico.
    1. Selecione **+ Novo gráfico** para ver vários gráficos numa só vista.
    1. Utilize o marcador de tempo para alterar o intervalo de tempo (predefinido: passados 24 horas).
    1. Utilizar [ **Adicionar filtro** e **aplicar a divisão** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) para métricas que tenham dimensões.
    1. Selecione **Pin to dashboard** para adicionar a configuração do gráfico aos painéis para que possa vê-lo novamente.
    1. Detete nova **regra de alerta** para visualizar as suas métricas utilizando os critérios definidos. A nova regra de alerta incluirá as dimensões do seu recurso alvo, métrica, divisão e filtro da sua ficha. Modifique estas definições no painel de [criação](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)de regras de alerta .

Informações adicionais sobre a utilização do Explorador de [Métricas.](/azure/azure-monitor/platform/metrics-getting-started)

## <a name="supported-azure-data-explorer-metrics"></a>Métricas de Data Explorer do Azure com suporte

As métricas de Data Explorer do Azure com suporte são separadas em várias categorias de acordo com o uso. 

### <a name="cluster-health-metrics"></a>Métricas de integridade do cluster

As métricas de integridade do cluster rastreiam a integridade geral do cluster. Isso inclui utilização e capacidade de resposta de recursos e ingestão.

**Métricas** | **Unidade** | **Agregação** | **Descrição métrica** | **Dimensões** |
|---|---|---|---|---|
| Utilização de cache | Percentagem | Avg, Max, Min | Percentagem dos recursos de cache atribuídos atualmente em uso pelo cluster. Cache é o tamanho do SSD atribuído à atividade do utilizador de acordo com a política de cache definida. Uma utilização média de cache de 80% ou menos é um estado sustentável para um cluster. Se a utilização média do cache for superior a 80%, o cluster deve ser [dimensionado até](manage-cluster-vertical-scaling.md) um nível de preços otimizado de armazenamento ou [dimensionado](manage-cluster-horizontal-scaling.md) para mais instâncias. Alternativamente, adapte a política de cache (menos dias em cache). Se a utilização do cache for superior a 100%, o tamanho dos dados a serem cache, de acordo com a política de cache, é maior que o tamanho total da cache no cluster. | Nenhuma |
| CPU | Percentagem | Avg, Max, Min | Percentagem dos recursos calculados atribuídos atualmente em uso por máquinas no cluster. Um CpU médio de 80% ou menos é sustentável para um cluster. O valor máximo da CPU é de 100%, o que significa que não existem recursos de computação adicionais para processar dados. Quando um cluster não estiver a funcionar bem, verifique o valor máximo do CPU para determinar se existem CPUs específicos que estão bloqueados. | Nenhuma |
| Utilização de ingestão | Percentagem | Avg, Max, Min | Percentagem de recursos reais utilizados para ingerir dados do total dos recursos atribuídos, na política de capacidade, para a realização da ingestão. A política de capacidade de incumprimento não é superior a 512 operações de ingestão simultâneas ou 75% dos recursos de cluster investidos na ingestão. A utilização média da ingestão de 80% ou menos é um estado sustentável para um cluster. O valor máximo da utilização da ingestão é de 100%, o que significa que toda a capacidade de ingestão de clusters é utilizada e pode resultar uma fila de ingestão. | Nenhuma |
| Mantenha-se vivo | Contagem | Rio Avg | Rastreia a capacidade de resposta do cluster. Um cluster totalmente responsivo devolve o valor 1 e um cluster bloqueado ou desligado retorna 0. |
| Número total de comandos estrangulados | Contagem | Avg, Max, Min, Sum | O número de comandos estrangulados (rejeitados) no cluster, uma vez que o número máximo permitido de comandos simultâneos (paralelos) foi atingido. | Nenhuma |
| Número total de extensões | Contagem | Avg, Max, Min, Sum | Número total de extensões de dados no cluster. As mudanças nesta métrica podem implicar mudanças maciças na estrutura de dados e alta carga no cluster, uma vez que a fusão das extensões de dados é uma atividade pesada de CPU. | Nenhuma |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportar métricas de integridade e desempenho

As métricas de desempenho e integridade de exportação controlam a integridade geral e o desempenho de operações de exportação, como atraso, resultados, número de registros e utilização.

**Métricas** | **Unidade** | **Agregação** | **Descrição métrica** | **Dimensões** |
|---|---|---|---|---|
Número de exportação contínuo de registros exportados    | Contagem | Soma | O número de registos exportados em todos os postos de trabalho de exportação contínuos. | Nenhuma |
Atraso máximo de exportação contínua |    Contagem   | Máx.   | O atraso (em minutos) relatado pelos contínuos trabalhos de exportação no cluster. | Nenhuma |
Contagem de exportação contínua pendente | Contagem | Máx.   | O número de postos de trabalho de exportação continuados pendentes. Estes postos de trabalho estão prontos para funcionar, mas esperam numa fila, possivelmente devido à falta de capacidade). 
Resultado da exportação contínua    | Contagem |   Contagem   | O resultado de falha/sucesso de cada exportação contínua. | Nome de Exportação Contínua |
Utilização das exportações |    Percentagem | Máx.   | Capacidade de exportação utilizada, fora da capacidade total de exportação do cluster (entre 0 e 100). | Nenhuma |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Métricas de desempenho e integridade de ingestão

As métricas de desempenho e integridade de ingestão rastreiam a integridade geral e o desempenho de operações de ingestão, como latência, resultados e volume.

**Métricas** | **Unidade** | **Agregação** | **Descrição métrica** | **Dimensões** |
|---|---|---|---|---|
| Eventos processados (para Hubs de Evento/IoT) | Contagem | Max, Min, Sum | Número total de eventos lidos a partir de centros de eventos e processados pelo cluster. Os eventos são divididos em eventos rejeitados e eventos aceites pelo motor de cluster. | EventStatus |
| Latência de ingestão | Segundos | Avg, Max, Min | Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até estar pronto para consulta. O período de latência depende do cenário de ingestão. | Nenhuma |
| Resultado da ingestão | Contagem | Contagem | Número total de operações de ingestão que falharam e conseguiram. Utilize **a divisão** para criar baldes de sucesso e falhar resultados e analisar as dimensões **(Valor** > **Estado).**| IngestionResultDetails |
| Volume de ingestão (em MB) | Contagem | Máximo, soma | O tamanho total dos dados ingeridos para o cluster (em MB) antes da compressão. | Base de Dados |
| | | | |  

### <a name="query-performance"></a>Desempenho de consultas

As métricas de desempenho de consulta rastreiam a duração da consulta e o número total de consultas simultâneas ou limitadas.

**Métricas** | **Unidade** | **Agregação** | **Descrição métrica** | **Dimensões** |
|---|---|---|---|---|
| Duração da consulta | Milissegundos | AVG, min, Max, Sum | O tempo total até que os resultados da consulta sejam recebidos (não inclui latência da rede). | ConsultaStatus |
| Número total de consultas simultâneas | Contagem | Avg, Max, Min, Sum | O número de consultas corre paralelamente no aglomerado. Esta métrica é uma boa maneira de estimar a carga no cluster. | Nenhuma |
| Número total de consultas estranguladas | Contagem | Avg, Max, Min, Sum | O número de consultas estranguladas (rejeitadas) no cluster. O número máximo de consultas simultâneas (paralelas) permitidas é definido na política de consulta simultânea. | Nenhuma |
| | | | |

### <a name="streaming-ingest-metrics"></a>Métricas de ingestão de streaming

As métricas de ingestão de streaming rastreiam dados de ingestão de streaming e taxa de solicitação, duração e resultados.

**Métricas** | **Unidade** | **Agregação** | **Descrição métrica** | **Dimensões** |
|---|---|---|---|---|
Taxa de dados de ingestão de streaming |    Contagem   | RateRequestsPerSecond | Volume total de dados ingeridos no cluster. | Nenhuma |
Duração do ingest de streaming   | Milissegundos  | Avg, Max, Min | Duração total de todas as solicitações de ingestão de streaming. | Nenhuma |
Taxa de pedido de ingestão de streaming   | Contagem | Count, Méd, Max, min, Sum | Número total de solicitações de ingestão de streaming. | Nenhuma |
Resultado da ingest de streaming | Contagem | Rio Avg   | Número total de solicitações de ingestão de streaming por tipo de resultado. | Resultado |
| | | | |

Informações adicionais sobre [métricas de cluster do Azure Data Explorer suportadas.](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)


## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Ingerir e consultar dados de monitorização no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Monitorize operações de ingestão do Explorador de Dados do Azure utilizando registos de diagnóstico](/azure/data-explorer/using-diagnostic-logs)
* [Início Rápido: consultar dados no Azure Data Explorer](web-query-data.md)
