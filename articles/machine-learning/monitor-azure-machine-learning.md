---
title: Monitorização da Aprendizagem automática Azure [ Microsoft Docs
description: Aprenda a usar o Monitor Azure para visualizar, analisar e criar alertas sobre métricas do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399101"
---
# <a name="monitoring-azure-machine-learning"></a>Monitorização da aprendizagem automática azure

Este artigo descreve os dados de monitorização gerados pela Azure Machine Learning. Também descreve como pode usar o Monitor Azure para analisar os seus dados e definir alertas.

> [!TIP]
> A informação neste documento é principalmente para administradores, uma vez que descreve a monitorização para o Azure Machine Learning. Se for um cientista de dados ou desenvolvedor e pretender monitorizar informações específicas das suas corridas de formação de modelos, consulte os seguintes documentos:
>
> * [Iniciar, monitorizar e cancelar treinos](how-to-manage-runs.md)
> * [Métricas de registo para execuções de preparações](how-to-track-experiments.md)
> * [Controlar experiências com o MLflow](how-to-use-mlflow.md)
> * [Visualizar execuções com TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

O Azure Machine Learning regista os dados de monitorização utilizando o Azure Monitor, que é um serviço de monitorização de pilhas completa seletiva em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure. Também pode monitorizar recursos em outras nuvens e no local.

Comece com o artigo Visão geral do [Monitor Azure,](/azure/azure-monitor/overview)que fornece uma visão geral das capacidades de monitorização. As seguintes secções baseiam-se nesta informação, fornecendo especificidades da utilização do Monitor Azure com o Azure Machine Learning.

Para compreender os custos associados ao Monitor Azure, consulte [a utilização e os custos estimados.](/azure/azure-monitor/platform/usage-estimated-costs) Para compreender o tempo que os seus dados demoram a aparecer no Monitor Azure, consulte o tempo de [ingestão](/azure/azure-monitor/platform/data-ingestion-time)de dados do Registo .

## <a name="monitoring-data-from-azure-machine-learning"></a>Dados de monitorização da Aprendizagem automática de Azure

O Azure Machine Learning recolhe os mesmos tipos de dados de monitorização que outros recursos do Azure, que são descritos na [Monitorização de dados a partir de recursos Azure.](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) Consulte a referência de dados de monitorização da [Aprendizagem automática Azure](monitor-resource-reference.md) para obter uma referência detalhada dos registos e métricas criados pela Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Analisar dados métricos

Pode analisar métricas para O Machine Learning Azure abrindo **métricas** do menu **Azure Monitor.** Consulte [Começar com o Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre a utilização desta ferramenta.

Todas as métricas para O Machine Learning Azure estão no espaço de trabalho do serviço de **aprendizagem automática.**

![Explorador de Métricas com espaço de trabalho do serviço de aprendizagem automática selecionado](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtragem e divisão

Para métricas que suportam dimensões, pode aplicar filtros utilizando um valor de dimensão. Por exemplo, filtrar **os Núcleos Ativos** para um Nome de **Cluster** de `cpu-cluster`. 

Também pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam uns aos outros. Por exemplo, dividir o **Pipeline Step Type** para ver uma contagem dos tipos de passos utilizados no gasoduto.

Para obter mais informações sobre filtragem e divisão, consulte [funcionalidades avançadas do Monitor Azure](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alertas

Pode aceder a alertas para o Azure Machine Learning abrindo **alertas** a partir do menu **Do Monitor Azure.** Consulte [Criar, visualizar e gerir alertas métricos utilizando](/azure/azure-monitor/platform/alerts-metric) o Monitor Azure para obter detalhes sobre a criação de alertas.

A tabela que se segue lista regras comuns e recomendadas de alerta métrico para a Aprendizagem automática de Azure:

| Tipo de alerta | Condição | Descrição |
|:---|:---|:---|
| Implementação do modelo falhou | Tipo de agregação: Total, Operador: Maior do que, Valor limiar: 0 | Quando uma ou mais implementações de modelos falharam |
| Percentagem de Utilização de Quotas | Tipo de agregação: Média, operador: Maior do que, Valor limiar: 90| Quando a percentagem de utilização de quotas for superior a 90% |
| Nós Inutilizáveis | Tipo de agregação: Total, Operador: Maior do que, Valor limiar: 0 | Quando há um ou mais nós inutilizáveis |

## <a name="configuration"></a>Configuração

> [!IMPORTANT]
> __As métricas para o Azure Machine Learning não precisam de ser configuradas,__ são recolhidas automaticamente e estão disponíveis no Metrics Explorer para monitorização e alerta.

Pode adicionar uma definição de diagnóstico para configurar a seguinte funcionalidade:

* Arquivo informação de registo e métricas para uma conta de armazenamento Azure.
* Transmita informação sobre o registo e métricas para um Hub de Eventos Azure.
* Envie informações de registo e métricas para o Azure Monitor Log Analytics.

Permitir estas definições requer serviços adicionais do Azure (conta de armazenamento, centro de eventos ou Log Analytics), o que pode aumentar o seu custo. Para calcular um custo estimado, visite a calculadora de [preços Azure.](https://azure.microsoft.com/pricing/calculator)

Para obter mais informações sobre a criação de uma definição de diagnóstico, consulte [Criar definição de diagnóstico para recolher registos e métricas](/azure/azure-monitor/platform/diagnostic-settings)da plataforma em Azure .

Pode configurar os seguintes registos para o Azure Machine Learning:

| Categoria | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters computacionais de Aprendizagem automática Azure. |
| AmlComputeClusterNodeEvent | Eventos de nós dentro de um cluster de computação azure machine learning. |
| AmlComputeJobEvent | Eventos de trabalhos em execução na computação Azure Machine Learning. |

> [!NOTE]
> Quando ativa as métricas numa definição de diagnóstico, a informação de dimensão não está atualmente incluída como parte da informação enviada para uma conta de armazenamento, centro de eventos ou análise de registo.

## <a name="analyzing-log-data"></a>Analisar dados de registo

Utilizar o Azure Monitor Log Analytics requer que crie uma configuração de diagnóstico e ative __enviar informações para registar o Analytics__. Para mais informações, consulte a secção [De Configuração.](#configuration)

Os dados em Registos do Monitor Azure são armazenados em tabelas, com cada tabela a ter o seu próprio conjunto de propriedades únicas. A Azure Machine Learning armazena dados nas seguintes tabelas:

| Tabela | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters computacionais de Aprendizagem automática Azure. |
| AmlComputeClusterNodeEvent | Eventos de nós dentro de um cluster de computação azure machine learning. |
| AmlComputeJobEvent | Eventos de trabalhos em execução na computação Azure Machine Learning. |

> [!IMPORTANT]
> Ao selecionar **Registos** do menu De aprendizagem automática Azure, o Log Analytics é aberto com o âmbito de consulta definido para o espaço de trabalho atual. Isto significa que as consultas de registo incluirão apenas dados desse recurso. Se pretender executar uma consulta que inclua dados de outras bases de dados ou dados de outros serviços do Azure, selecione **Registos** do menu **Do Monitor Do Azure.** Consulte o âmbito de consulta de registo e o intervalo de [tempo no Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) para obter mais detalhes.

Para obter uma referência detalhada dos registos e métricas, consulte a referência de dados de monitorização do [Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-queries"></a>Consultas de exemplo

Seguem-se as consultas que pode utilizar para o ajudar a monitorizar os seus recursos de Aprendizagem automática Azure: 

+ Conseguir empregos falhados nos últimos cinco dias:

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

+ Obtenha eventos de cluster nos últimos cinco dias para clusters onde o tamanho vm é Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Obtenha nósalizadores nos últimos oito dias:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência dos registos e métricas, consulte a referência de dados de monitorização da [Aprendizagem automática do Azure](monitor-resource-reference.md).
- Para obter informações sobre o trabalho com quotas relacionadas com o Azure Machine Learning, consulte [Gerir e solicitar quotas para os recursos Azure.](how-to-manage-quotas.md)
- Para mais informações sobre a monitorização dos recursos do Azure, consulte [a Monitorização dos recursos do Azure com o Monitor Azure.](/azure/azure-monitor/insights/monitor-azure-resource)
