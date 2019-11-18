---
title: Azure Machine Learning de monitoramento | Microsoft Docs
description: Saiba como usar Azure Monitor para exibir, analisar e criar alertas sobre as métricas de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/04/2019
ms.openlocfilehash: 0a28292ac95dca7b46d9f82c32b9881c18d74b87
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123537"
---
# <a name="monitoring-azure-machine-learning"></a>Azure Machine Learning de monitoramento

Este artigo descreve os dados de monitoramento gerados pelo Azure Machine Learning. Ele também descreve como você pode usar o Azure Monitor para analisar seus dados e definir alertas.

> [!TIP]
> As informações contidas neste documento são basicamente para administradores, pois descrevem o monitoramento do serviço de Azure Machine Learning. Se você for um cientista de dados ou desenvolvedor e quiser monitorar informações específicas para suas execuções de treinamento de modelo, consulte os seguintes documentos:
>
> * [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md)
> * [Métricas de log para execuções de treinamento](how-to-track-experiments.md)
> * [Rastrear eexperiments com MLflow](how-to-use-mlflow.md)
> * [Visualizar é executado com TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning registra dados de monitoramento usando Azure Monitor, que é um serviço de monitoramento de pilha completo no Azure. Azure Monitor fornece um conjunto completo de recursos para monitorar os recursos do Azure. Ele também pode monitorar recursos em outras nuvens e locais.

Comece com o artigo [Azure monitor visão geral](/azure/azure-monitor/overview), que fornece uma visão geral dos recursos de monitoramento. As seções a seguir se baseiam nessas informações, fornecendo especificações OO usando Azure Monitor com Azure Machine Learning.

Para entender os custos associados a Azure Monitor, confira [uso e custos estimados](/azure/azure-monitor/platform/usage-estimated-costs). Para entender o tempo que leva para que seus dados apareçam em Azure Monitor, consulte [tempo de ingestão de dados de log](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorando dados de Azure Machine Learning

Azure Machine Learning coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Veja [Azure Machine Learning referência de dados de monitoramento](monitor-resource-reference.md) para obter uma referência detalhada dos logs e métricas criados por Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Analisando dados de métrica

Você pode analisar as métricas para Azure Machine Learning abrindo as **métricas** no menu **Azure monitor** . Consulte [introdução ao Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre como usar essa ferramenta.

Todas as métricas para Azure Machine Learning estão no namespace **Machine Learning espaço de trabalho do serviço**.

![Metrics Explorer com espaço de trabalho de serviço do Machine Learning selecionado](media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtragem e divisão

Para métricas que dão suporte a dimensões, você pode aplicar filtros usando um valor de dimensão. Por exemplo, a filtragem de **núcleos ativos** para um **nome de cluster** `cpu-cluster`. 

Você também pode dividir uma métrica por dimensão para visualizar como os segmentos diferentes da métrica se comparam entre si. Por exemplo, dividindo o **tipo de etapa de pipeline** para ver uma contagem dos tipos de etapas usados no pipeline.

Para obter mais informações sobre filtragem e divisão, consulte [recursos avançados do Azure monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alertas

Você pode acessar alertas para Azure Machine Learning abrindo **alertas** no menu **Azure monitor** . Consulte [criar, exibir e gerenciar alertas de métricas usando Azure monitor](/azure/azure-monitor/platform/alerts-metric) para obter detalhes sobre como criar alertas.

A tabela a seguir lista as regras de alerta de métrica comuns e recomendadas para Azure Machine Learning:

| Tipo de alerta | Condição | Descrição |
|:---|:---|:---|
| Falha na Implantação de Modelo | Tipo de agregação: total, operador: maior que, valor do limite: 0 | Quando uma ou mais implantações de modelo falharam |
| Porcentagem de utilização de cota | Tipo de agregação: Average, Operator: maior que, valor de limite: 90| Quando a porcentagem de utilização de cota é maior que 90% |
| Nós inutilizáveis | Tipo de agregação: total, operador: maior que, valor do limite: 0 | Quando há um ou mais nós inutilizáveis |

## <a name="configuration"></a>Configuração

> [!IMPORTANT]
> As __métricas para Azure Machine Learning não precisam ser configuradas__, elas são coletadas automaticamente e estão disponíveis no Metrics Explorer para monitoramento e alertas.

Você pode adicionar uma configuração de diagnóstico para configurar a seguinte funcionalidade:

* Arquive informações de log e métricas para uma conta de armazenamento do Azure.
* Transmita informações de log e métricas para um hub de eventos do Azure.
* Envie informações de log e métricas para Azure Monitor Log Analytics.

A habilitação dessas configurações requer serviços adicionais do Azure (conta de armazenamento, Hub de eventos ou Log Analytics), o que pode aumentar seu custo. Para calcular um custo estimado, visite a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).

Para obter mais informações sobre como criar uma configuração de diagnóstico, consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](/azure/azure-monitor/platform/diagnostic-settings).

Você pode configurar os seguintes logs para Azure Machine Learning:

| Categoria | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters de computação Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Eventos de nós em um cluster de computação Azure Machine Learning. |
| AmlComputeJobEvent | Eventos de trabalhos em execução no Azure Machine Learning computação. |

> [!NOTE]
> Quando você habilita as métricas em uma configuração de diagnóstico, as informações de dimensão não são incluídas atualmente como parte das informações enviadas para uma conta de armazenamento, Hub de eventos ou log Analytics.

## <a name="analyzing-log-data"></a>Analisando dados de log

Usando Azure Monitor Log Analytics exige que você crie uma configuração de diagnóstico e habilite __as informações de envio para log Analytics__. Para obter mais informações, consulte a seção [configuração](#configuration) .

Os dados em logs de Azure Monitor são armazenados em tabelas, sendo que cada tabela tem seu próprio conjunto de propriedades exclusivas. Azure Machine Learning armazena dados nas tabelas a seguir:

| Tabela | Descrição |
|:---|:---|
| AmlComputeClusterEvent | Eventos de clusters de computação Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Eventos de nós em um cluster de computação Azure Machine Learning. |
| AmlComputeJobEvent | Eventos de trabalhos em execução no Azure Machine Learning computação. |

> [!IMPORTANT]
> Quando você seleciona **logs** no menu Azure Machine Learning, log Analytics é aberto com o escopo de consulta definido como o espaço de trabalho atual. Isso significa que as consultas de log incluirão apenas os dados desse recurso. Se você quiser executar uma consulta que inclua dados de outros bancos de dados ou de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Consulte [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](/azure/azure-monitor/log-query/scope/) para obter detalhes.

Para obter uma referência detalhada dos logs e métricas, consulte [Azure Machine Learning referência de dados de monitoramento](monitor-resource-reference.md).

### <a name="sample-queries"></a>Amostras de consultas

Veja a seguir as consultas que você pode usar para ajudá-lo a monitorar seus Azure Machine Learning recursos: 

+ Obter trabalhos com falha nos últimos cinco dias:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obter registros para um nome de trabalho específico:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obter eventos de cluster nos últimos cinco dias para clusters em que o tamanho da VM é Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Obter nós alocados nos últimos oito dias:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência dos logs e métricas, consulte [Azure Machine Learning referência de dados de monitoramento](monitor-resource-reference.md).
- Para obter informações sobre como trabalhar com cotas relacionadas a Azure Machine Learning, consulte [gerenciar e solicitar cotas para recursos do Azure](how-to-manage-quotas.md).
- Para obter detalhes sobre como monitorar os recursos do Azure, consulte [monitorando recursos do Azure com o Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource).
