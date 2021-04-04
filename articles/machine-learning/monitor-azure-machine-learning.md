---
title: Monitorização da aprendizagem da máquina de Azure | Microsoft Docs
description: Aprenda a usar o Azure Monitor para ver, analisar e criar alertas sobre métricas da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: a18ee02b5e91b628a25655949a652270bd7436c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100575145"
---
# <a name="monitor-azure-machine-learning"></a>Monitorizar o Azure Machine Learning

Quando você tem aplicações críticas e processos de negócio contando com recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pela Azure Machine Learning e como analisar e alertar sobre estes dados com o Azure Monitor.

> [!TIP]
> A informação neste documento destina-se principalmente aos __administradores,__ uma vez que descreve a monitorização do serviço de Aprendizagem automática Azure e dos serviços Azure associados. Se for cientista ou __desenvolvedor de__ __dados,__ e quiser monitorizar informações específicas das *suas séries de formação de modelos,* consulte os seguintes documentos:
>
> * [Iniciar, monitorizar e cancelar treinos](how-to-manage-runs.md)
> * [Métricas de registo para execuções de preparações](how-to-track-experiments.md)
> * [Controlar experiências com o MLflow](how-to-use-mlflow.md)
> * [Visualizar execuções com TensorBoard](how-to-monitor-tensorboard.md)
>
> Se pretender monitorizar as informações geradas por modelos implementados como serviços web ou módulos IoT Edge, consulte [recolher dados do modelo](how-to-enable-data-collection.md) e [monitorar com insights de aplicações](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Machine Learning cria dados de monitorização utilizando [o Azure Monitor,](../azure-monitor/overview.md)que é um serviço completo de monitorização de pilhas em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure. Também pode monitorizar recursos em outras nuvens e no local.

Comece com o artigo [Monitorar recursos Azure com Azure Monitor,](../azure-monitor/essentials/monitor-azure-resource.md)que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As secções seguintes baseiam-se neste artigo descrevendo os dados específicos recolhidos para a Azure Machine Learning. Estas secções também fornecem exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

> [!TIP]
> Para compreender os custos associados ao Azure Monitor, consulte [a Utilização e os custos estimados.](../azure-monitor//usage-estimated-costs.md) Para compreender o tempo que os seus dados demoram a aparecer no Azure Monitor, consulte [o tempo de ingestão de dados do Registo](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-machine-learning"></a>Dados de monitorização da Azure Machine Learning

A Azure Machine Learning recolhe os mesmos tipos de dados de monitorização que outros recursos Azure que são descritos na [monitorização de dados a partir de recursos Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte [a referência de dados de monitorização da Azure Machine Learning](monitor-resource-reference.md) para uma referência detalhada dos registos e métricas criados pela Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma e o registo de Atividade são recolhidos e armazenados automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico.  

Os Registos de Recursos não são recolhidos e armazenados até criar uma definição de diagnóstico e encaminhá-los para um ou mais locais.

Consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../azure-monitor/essentials/diagnostic-settings.md) para o processo detalhado para criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell. Quando cria uma definição de diagnóstico, especifica quais as categorias de registos a recolher. As categorias de Azure Machine Learning estão listadas na [referência de dados de monitorização de machine learning do Azure.](monitor-resource-reference.md#resource-logs)

> [!IMPORTANT]
> Ativar estas definições requer serviços Azure adicionais (conta de armazenamento, centro de eventos ou Log Analytics), que podem aumentar o seu custo. Para calcular um custo estimado, visite a [calculadora de preços da Azure.](https://azure.microsoft.com/pricing/calculator)

Pode configurar os seguintes registos para Azure Machine Learning:

| Categoria | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters compute Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Eventos de nós dentro de um cluster compute compute Azure Machine Learning. |
| AmlComputeJobEvent | Eventos de empregos em execução no cálculo Azure Machine Learning. |

> [!NOTE]
> Quando ativa métricas numa definição de diagnóstico, a informação de dimensão não está atualmente incluída como parte da informação enviada para uma conta de armazenamento, centro de eventos ou análise de registo.

As métricas e registos que pode recolher são discutidos nas seguintes secções.

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para Azure Machine Learning, juntamente com métricas de outros serviços Azure, abrindo **métricas** a partir do menu **Azure Monitor.** Consulte [o Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) para obter detalhes sobre a utilização desta ferramenta.

Para obter uma lista das métricas da plataforma recolhidas, consulte [as métricas de referência de dados de aprendizagem automática do Azure](monitor-resource-reference.md#metrics)Machine Learning .

Todas as métricas para Azure Machine Learning estão no espaço de trabalho do **serviço de aprendizagem automática** namespace.

![Métricas Explorer com espaço de trabalho de serviço de aprendizagem automática selecionado](./media/monitor-azure-machine-learning/metrics.png)

Para referência, pode ver uma lista de [todas as métricas de recursos suportadas no Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Os dados das métricas do Azure Monitor estão disponíveis durante 90 dias. No entanto, ao criar gráficos apenas 30 dias podem ser visualizados. Por exemplo, se quiser visualizar um período de 90 dias, deve quebrá-lo em três gráficos de 30 dias dentro do período de 90 dias.
### <a name="filtering-and-splitting"></a>Filtragem e divisão

Para métricas que suportam dimensões, pode aplicar filtros usando um valor de dimensão. Por exemplo, filtrar **núcleos ativos** para um nome de **cluster** de `cpu-cluster` . 

Também pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam entre si. Por exemplo, dividir o **Pipeline Step Type** para ver uma contagem dos tipos de passos utilizados no gasoduto.

Para obter mais informações sobre filtragem e divisão, consulte [as funcionalidades avançadas do Azure Monitor](../azure-monitor/essentials/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Análise de registos

A utilização do Azure Monitor Log Analytics requer que crie uma configuração de diagnóstico e __adisse Enviar informações para registar análises.__ Para mais informações, consulte a [secção Recolha e encaminhamento.](#collection-and-routing)

Os dados em Registos monitores Azure são armazenados em tabelas, com cada mesa a ter o seu próprio conjunto de propriedades únicas. A Azure Machine Learning armazena dados nas seguintes tabelas:

| Tabela | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters compute Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Eventos de nós dentro de um cluster compute compute Azure Machine Learning. |
| AmlComputeJobEvent | Eventos de empregos em execução no cálculo Azure Machine Learning. |

> [!IMPORTANT]
> Quando seleciona **Logs** do menu Azure Machine Learning, o Log Analytics é aberto com o âmbito de consulta definido para o espaço de trabalho atual. Isto significa que as consultas de registo só incluirão dados desse recurso. Se pretender executar uma consulta que inclua dados de outras bases de dados ou dados de outros serviços Azure, selecione **Registos** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](../azure-monitor/logs/scope.md) para obter mais detalhes.

Para obter uma referência detalhada dos registos e métricas, consulte [a referência de dados de monitorização da Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Experimente consultas kusto

> [!IMPORTANT]
> Quando seleciona **Logs** do menu [nome de serviço], o Log Analytics é aberto com o âmbito de consulta definido para o atual espaço de trabalho de Aprendizagem automática Azure. Isto significa que as consultas de registo só incluirão dados desse recurso. Se pretender executar uma consulta que inclua dados de outros espaços de trabalho ou dados de outros serviços Azure, selecione **Logs** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](../azure-monitor/logs/scope.md) para obter mais detalhes.

Seguem-se as consultas que pode utilizar para o ajudar a monitorizar os seus recursos de Aprendizagem automática Azure: 

+ Arranja empregos falhados nos últimos cinco dias:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obtenha registos de um nome de trabalho específico:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obtenha eventos de cluster nos últimos cinco dias para clusters onde o tamanho VM é Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Receba os nóns atribuídos nos últimos oito dias:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Alertas

Pode aceder a alertas para a aprendizagem automática Azure, abrindo **alertas** a partir do menu **Azure Monitor.** Consulte [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](../azure-monitor/alerts/alerts-metric.md) para obter detalhes sobre a criação de alertas.

As seguintes listas de tabelas são comuns e recomendadas regras de alerta métrico para a aprendizagem automática Azure:

| Tipo de alerta | Condição | Description |
|:---|:---|:---|
| Implementação de modelo falhou | Tipo de agregação: Total, Operador: Maior do que, Valor limiar: 0 | Quando uma ou mais implementações de modelos falharam |
| Percentagem de Utilização de Cots | Tipo de agregação: Média, Operador: Maior do que, Valor limiar: 90| Quando a percentagem de utilização de quota é superior a 90% |
| Nódes Inutilizáveis | Tipo de agregação: Total, Operador: Maior do que, Valor limiar: 0 | Quando há um ou mais nódoas inutilizáveis |

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência dos registos e métricas, consulte [a referência de dados de Aprendizagem automática Azure.](monitor-resource-reference.md)
- Para obter informações sobre o trabalho com quotas relacionadas com a Azure Machine Learning, consulte [Gerir e solicitar quotas para recursos Azure.](how-to-manage-quotas.md)
- Para obter informações sobre a monitorização dos recursos do Azure, consulte [os recursos do Azure monitorando com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
