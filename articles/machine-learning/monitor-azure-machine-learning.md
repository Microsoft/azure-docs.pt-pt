---
title: Monitorização da Aprendizagem automática do Azure Microsoft Docs
description: Aprenda a usar o Azure Monitor para ver, analisar e criar alertas sobre métricas da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78399101"
---
# <a name="monitoring-azure-machine-learning"></a>Monitorização da aprendizagem da máquina de Azure

Este artigo descreve os dados de monitorização gerados pela Azure Machine Learning. Também descreve como pode utilizar o Monitor Azure para analisar os seus dados e definir alertas.

> [!TIP]
> A informação neste documento é principalmente para administradores, uma vez que descreve a monitorização para o Azure Machine Learning. Se é cientista ou desenvolvedor de dados e pretende monitorizar informações específicas das suas séries de formação de modelos, consulte os seguintes documentos:
>
> * [Iniciar, monitorizar e cancelar treinos](how-to-manage-runs.md)
> * [Métricas de registo para execuções de preparações](how-to-track-experiments.md)
> * [Controlar experiências com o MLflow](how-to-use-mlflow.md)
> * [Visualizar execuções com TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning regista dados de monitorização usando o Azure Monitor, que é um serviço completo de monitorização de pilhas em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure. Também pode monitorizar recursos em outras nuvens e no local.

Comece com o artigo [Azure Monitor visão geral](/azure/azure-monitor/overview), que fornece uma visão geral das capacidades de monitorização. As seguintes secções baseiam-se nesta informação, fornecendo especificidades da utilização do Azure Monitor com Azure Machine Learning.

Para compreender os custos associados ao Azure Monitor, consulte [a Utilização e os custos estimados.](/azure/azure-monitor/platform/usage-estimated-costs) Para compreender o tempo que os seus dados demoram a aparecer no Azure Monitor, consulte [o tempo de ingestão de dados do Registo](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Dados de monitorização da Azure Machine Learning

A Azure Machine Learning recolhe os mesmos tipos de dados de monitorização que outros recursos Azure, que são descritos na [monitorização de dados a partir de recursos Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Consulte [a referência de dados de monitorização da Azure Machine Learning](monitor-resource-reference.md) para uma referência detalhada dos registos e métricas criados pela Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Análise de dados métricos

Pode analisar métricas para Azure Machine Learning abrindo **métricas** a partir do menu **Azure Monitor.** Consulte [o Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre a utilização desta ferramenta.

Todas as métricas para Azure Machine Learning estão no espaço de trabalho do **serviço de aprendizagem automática**namespace.

![Métricas Explorer com espaço de trabalho de serviço de aprendizagem automática selecionado](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtragem e divisão

Para métricas que suportam dimensões, pode aplicar filtros usando um valor de dimensão. Por exemplo, filtrar **núcleos ativos** para um nome de **cluster** de `cpu-cluster` . 

Também pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam entre si. Por exemplo, dividir o **Pipeline Step Type** para ver uma contagem dos tipos de passos utilizados no gasoduto.

Para obter mais informações sobre filtragem e divisão, consulte [as funcionalidades avançadas do Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alertas

Pode aceder a alertas para a aprendizagem automática Azure, abrindo **alertas** a partir do menu **Azure Monitor.** Consulte [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](/azure/azure-monitor/platform/alerts-metric) para obter detalhes sobre a criação de alertas.

As seguintes listas de tabelas são comuns e recomendadas regras de alerta métrico para a aprendizagem automática Azure:

| Tipo de alerta | Condição | Descrição |
|:---|:---|:---|
| Implementação de modelo falhou | Tipo de agregação: Total, Operador: Maior do que, Valor limiar: 0 | Quando uma ou mais implementações de modelos falharam |
| Percentagem de Utilização de Cots | Tipo de agregação: Média, Operador: Maior do que, Valor limiar: 90| Quando a percentagem de utilização de quota é superior a 90% |
| Nódes Inutilizáveis | Tipo de agregação: Total, Operador: Maior do que, Valor limiar: 0 | Quando há um ou mais nódoas inutilizáveis |

## <a name="configuration"></a>Configuração

> [!IMPORTANT]
> __As métricas para a Aprendizagem automática Azure não precisam de ser configuradas,__ são recolhidas automaticamente e estão disponíveis no Metrics Explorer para monitorização e alerta.

Pode adicionar uma definição de diagnóstico para configurar a seguinte funcionalidade:

* Arquivar informações de registo e métricas para uma conta de armazenamento Azure.
* Transmita informações de registo e métricas para um Hub de Eventos Azure.
* Envie informações de registo e métricas para Azure Monitor Log Analytics.

Ativar estas definições requer serviços Azure adicionais (conta de armazenamento, centro de eventos ou Log Analytics), que podem aumentar o seu custo. Para calcular um custo estimado, visite a [calculadora de preços da Azure.](https://azure.microsoft.com/pricing/calculator)

Para obter mais informações sobre a criação de uma definição de diagnóstico, consulte [criar a definição de diagnóstico para recolher registos e métricas da plataforma em Azure](/azure/azure-monitor/platform/diagnostic-settings).

Pode configurar os seguintes registos para Azure Machine Learning:

| Categoria | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters compute Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Eventos de nós dentro de um cluster compute compute Azure Machine Learning. |
| AmlComputeJobEvent | Eventos de empregos em execução no cálculo Azure Machine Learning. |

> [!NOTE]
> Quando ativa métricas numa definição de diagnóstico, a informação de dimensão não está atualmente incluída como parte da informação enviada para uma conta de armazenamento, centro de eventos ou análise de registo.

## <a name="analyzing-log-data"></a>Análise de dados de registo

A utilização do Azure Monitor Log Analytics requer que crie uma configuração de diagnóstico e __adisse Enviar informações para registar análises.__ Para mais informações, consulte a secção [Configuração.](#configuration)

Os dados em Registos monitores Azure são armazenados em tabelas, com cada mesa a ter o seu próprio conjunto de propriedades únicas. A Azure Machine Learning armazena dados nas seguintes tabelas:

| Tabela | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters compute Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Eventos de nós dentro de um cluster compute compute Azure Machine Learning. |
| AmlComputeJobEvent | Eventos de empregos em execução no cálculo Azure Machine Learning. |

> [!IMPORTANT]
> Quando seleciona **Logs** do menu Azure Machine Learning, o Log Analytics é aberto com o âmbito de consulta definido para o espaço de trabalho atual. Isto significa que as consultas de registo só incluirão dados desse recurso. Se pretender executar uma consulta que inclua dados de outras bases de dados ou dados de outros serviços Azure, selecione **Registos** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) para obter mais detalhes.

Para obter uma referência detalhada dos registos e métricas, consulte [a referência de dados de monitorização da Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-queries"></a>Consultas de exemplo

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

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência dos registos e métricas, consulte [a referência de dados de monitorização da Azure Machine Learning](monitor-resource-reference.md).
- Para obter informações sobre o trabalho com quotas relacionadas com a Azure Machine Learning, consulte [Gerir e solicitar quotas para recursos Azure.](how-to-manage-quotas.md)
- Para obter informações sobre a monitorização dos recursos do Azure, consulte [os recursos do Azure monitorando com o Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
