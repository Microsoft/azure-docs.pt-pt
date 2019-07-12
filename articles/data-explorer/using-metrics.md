---
title: Monitorizar o desempenho, a Estado de funcionamento e a utilização com métricas de Explorador de dados do Azure
description: Saiba como utilizar as métricas do Explorador de dados do Azure para monitorizar o desempenho, a integridade e a utilização do cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: cb59fa0fe9094943dfc942d1d6e664891996c9e3
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67569288"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorizar o desempenho, a Estado de funcionamento e a utilização com métricas de Explorador de dados do Azure

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Explorador de dados do Azure, primeiro cria um cluster e criar um ou mais bases de dados desse cluster. Em seguida, ingerir (carregar) dados numa base de dados para que pode executar consultas em relação a ele. Métricas do Explorador de dados do Azure fornecem principais indicadores sobre o estado de funcionamento e o desempenho dos recursos de cluster. Utilize as métricas que estão descritas neste artigo para monitorizar o estado de funcionamento do Explorador de dados do Azure cluster e o desempenho no seu cenário específico, como métricas de autónomo. Também pode utilizar métricas como base para operacional [Dashboards do Azure](/azure/azure-portal/azure-portal-dashboards) e [alertas do Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).

* Criar uma [cluster e a base de dados](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Uso de métricas

No seu cluster do Explorador de dados do Azure, selecione **métricas** para abrir o painel de métricas e iniciar a análise no seu cluster.

![Selecione as métricas](media/using-metrics/select-metrics.png)

No painel de métricas:

![Painel de métricas](media/using-metrics/metrics-pane.png)

1. Para criar um gráfico de métricas, selecione **métrica** nome e relevantes **agregação** por métrica conforme especificado abaixo. O **Resource** e **espaço de nomes de métrica** selecionadores são pré-selecionada ao seu cluster do Explorador de dados do Azure.

    **Métricas** | **Unit** | **Agregação** | **Descrição de métrica**
    |---|---|---|---|
    | Utilização da cache | Percent | AVG, Max, Min | Percentagem de recursos de cache alocados atualmente em uso pelo cluster. Cache refere-se ao tamanho de SSD alocado para a atividade do utilizador, de acordo com a política de cache definida. Uma utilização média da cache de 80% ou inferior é um Estado sustentável para um cluster. Se a utilização de cache média for superior a 80%, o cluster deve estar [aumentado verticalmente](manage-cluster-vertical-scaling.md) a um armazenamento com otimização de escalão de preço ou [aumentados horizontalmente](manage-cluster-horizontal-scaling.md) para mais instâncias. Em alternativa, adaptar-se a política de cache (menos dias em cache). Se a utilização de cache é superior a 100%, o tamanho dos dados sejam armazenados em cache, de acordo com a política de colocação em cache, é maior do que o tamanho total da cache no cluster. |
    | CPU | Percent | AVG, Max, Min | Percentagem de recursos de computação alocados atualmente em utilização por máquinas no cluster. Uma CPU média de 80% ou inferior é sustentável para um cluster. O valor máximo de CPU é 100%, que significa que existem não existem recursos de computação adicionais para processar dados. Quando um cluster não está a executar corretamente, verifique o valor máximo da CPU para determinar se existem CPUs específicas que são bloqueados. |
    | Eventos processados (para os Hubs de eventos) | Count | Max, Min, soma | Número total de eventos leem os hubs de eventos e processados pelo cluster. Os eventos são divididos em eventos rejeitados e eventos aceites pelo mecanismo de cluster. |
    | Latência de ingestão | Segundos | AVG, Max, Min | Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até que ele está pronto para consulta. O período de latência de ingestão depende do cenário de ingestão. |
    | Resultado de ingestão | Count | Count | Número total de operações de ingestão que falhou e foi concluída com êxito. Uso **aplicam-se de que a divisão** para criar registos de sucesso e falha resultados e analisar as dimensões (**valor** > **estado**).|
    | Utilização de ingestão | Percent | AVG, Max, Min | Percentagem de recursos reais usados para ingerir dados de total de recursos alocados, na política de capacidade, para efetuar a ingestão. A política de capacidade predefinida é não mais de 512 operações de ingestão em simultâneo ou 75% dos recursos de cluster investidos em ingestão. Utilização média de ingestão de 80% ou inferior é um Estado sustentável para um cluster. O valor máximo da utilização de ingestão é 100%, o que significa que todas as capacidade de ingestão de cluster é usada e pode resultar numa fila de ingestão. |
    | Volume de ingestão (em MB) | Count | Max, Min, soma | O tamanho total dos dados ingeridos para o cluster (em MB) antes da compressão. |
    | Keep alive de | Count | Avg | Controla a capacidade de resposta do cluster. Um cluster totalmente reativo devolve o valor de 1 e um cluster de bloqueados ou desconectado retorna 0. |
    | Duração da consulta | Segundos | Contagem, média, Mín, Máx, soma | Total de tempo até que os resultados da consulta são recebidos (não inclui a latência de rede). |
    | | | |

    Informações adicionais relativamente à [suportado métricas de cluster do Explorador de dados do Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Selecione o **adicionar métrica** botão para ver várias métricas desenhadas no mesmo gráfico.
3. Selecione o **+ novo gráfico** botão para ver vários gráficos numa vista.
4. Utilize o Seletor de tempo para alterar o intervalo de tempo (predefinição: últimas 24 horas).
5. Uso [ **Adicionar filtro** e **aplicam-se de que a divisão** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) para métricas que têm dimensões.
6. Selecione **afixar ao dashboard** para adicionar a configuração de gráfico para os dashboards para que pode vê-lo novamente.
7. Definir **nova regra de alerta** para visualizar as métricas utilizando os critérios de conjunto. A nova regra de alerta irá incluir o recurso de destino, métrica, divisão e dimensões de filtro de gráfico. Modificar estas definições na [painel de criação de regra de alerta](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informações adicionais sobre como utilizar o [Explorador de métricas](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Consultar dados no Explorador de dados do Azure](web-query-data.md)
