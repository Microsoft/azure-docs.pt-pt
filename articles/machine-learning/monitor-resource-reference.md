---
title: Monitor Azure Machine Learning referência de dados Microsoft Docs
description: Material de referência importante necessário quando monitoriza a aprendizagem automática do Azure. Conheça os dados e recursos recolhidos para a Azure Machine Learning e disponível no Azure Monitor. O Azure Monitor recolhe e sobrea parece dados sobre o seu espaço de trabalho de aprendizagem de máquinas Azure, e permite-lhe visualizar métricas, definir alertas e analisar dados registados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/02/2020
ms.openlocfilehash: edd2b3e02c1a768b1f18a62faaf9b59539b92774
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739141"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitorização da referência de dados de aprendizagem de máquinas Azure

Conheça os dados e recursos recolhidos pelo Azure Monitor a partir do seu espaço de trabalho Azure Machine Learning. Consulte [a Monitorização da Aprendizagem automática Azure](monitor-azure-machine-learning.md) para obter detalhes sobre a recolha e análise de dados de monitorização.

## <a name="metrics"></a>Métricas

Esta secção lista todas as métricas da plataforma recolhidas automaticamente para a Azure Machine Learning. O fornecedor de recursos para estas métricas é [o Microsoft.MachineLearningServices/workspaces](/azure/azure-monitor/platform/metrics-supported#microsoftmachinelearningservicesworkspaces).

**Modelo**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| A implementação do modelo falhou | de palavras | O número de implementações de modelos que falharam. |
| Implementação de modelos iniciado | de palavras | O número de implementações de modelos começou. |
| Implementação de modelos conseguiu | de palavras | O número de implementações de modelos que conseguiram. |
| Registo modelo falhou | de palavras | O número de registos de modelos que falharam. |
| Registo modelo conseguiu | de palavras | O número de registos de modelos que se sucederam. |

**Quota**

A informação sobre quotas é apenas para o cálculo Azure Machine Learning.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Núcleos ativos | de palavras | O número de núcleos de computação ativo. |
| Nódes ativos | de palavras | O número de nós ativos. |
| Núcleos ociosos | de palavras | O número de núcleos de computação ocioso. |
| Nódoas ociosas | de palavras | O número de nós de computação ocioso. |
| Deixando núcleos | de palavras | O número de núcleos de saída. |
| Deixando os nódoas | de palavras | O número de nós de saída. |
| Núcleos preemptidos | de palavras | O número de núcleos preempted. |
| Nódoas presas | de palavras | O número de nós preempted. |
| Percentagem de utilização de quotas | Percentagem | A percentagem de quota utilizada. |
| Total de núcleos | de palavras | Os núcleos totais. |
| Nódoas totais | de palavras | Os nós totais. |
| Núcleos inutilizáveis | de palavras | O número de núcleos inutilizáveis. |
| Nódes inutilizáveis | de palavras | O número de nós inutilizáveis. |

**Recurso**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| CpuUtilização | Percentagem | Quanto por cento do CPU foi usado para um dado nó durante uma corrida/trabalho. Esta métrica só é publicada quando um trabalho está a funcionar num nó. Um trabalho pode usar um ou mais nós. Esta métrica é publicada por nó. |
| GpuUtilização | Percentagem | Quanto percentagem de GPU foi utilizada para um dado nó durante uma corrida/trabalho. Um nó pode ter uma ou mais GPUs. Esta métrica é publicada por GPU por nó. |

**Executar**

Informação sobre treinos.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Execuções concluídas | de palavras | O número de execuções completas. |
| Corridas falhadas | de palavras | O número de corridas falhadas. |
| Começou a correr | de palavras | O número de corridas iniciadas. |

## <a name="metric-dimensions"></a>Dimensões métricas

Para obter mais informações sobre as dimensões métricas, consulte [métricas multidimensionais.](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)

A Azure Machine Learning tem as seguintes dimensões associadas às suas métricas.

| Dimensão | Descrição |
| ---- | ---- |
| Nome do Cluster | O nome da instância computacional. Disponível para todas as métricas de quota. |
| Nome da família VM | O nome da família VM usado pelo aglomerado. Disponível para percentagem de utilização de cots. |
| Prioridade Vm | A prioridade do VM. Disponível para percentagem de utilização de cots.
| CreatedTime | Disponível apenas para CpuUtilization e GpuUtilization. |
| DeviceId | ID do dispositivo (GPU). Disponível apenas para gpuutilização. |
| NodeId | Identificação do nó criado onde o trabalho está a funcionar. Disponível apenas para CpuUtilization e GpuUtilization. |
| RunId | Identificação da corrida/trabalho. Disponível apenas para CpuUtilization e GpuUtilization. |
| ComputeType | O tipo de cálculo que a corrida usou. Apenas disponível para execuções concluídas, corridas falhadas e execuções iniciadas. |
| PipelineStepType | O tipo de [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py&preserve-view=true) usado na corrida. Apenas disponível para execuções concluídas, corridas falhadas e execuções iniciadas. |
| PublishedPipelineId | A identificação do oleoduto publicado usado na corrida. Apenas disponível para execuções concluídas, corridas falhadas e execuções iniciadas. |
| RunType | O tipo de corrida. Apenas disponível para execuções concluídas, corridas falhadas e execuções iniciadas. |

Os valores válidos para a dimensão RunType são:

| Valor | Descrição |
| ----- | ----- |
| Experimentação | Não oleoduto funciona. |
| PipelineRun | Um oleoduto, que é o pai de um StepRun. |
| StepRun | Uma corrida para um passo de oleoduto. |
| ReusedStepRun | Uma corrida para um passo de oleoduto que reutiliza uma corrida anterior. |

## <a name="activity-log"></a>Registo de atividades

A tabela que se segue lista as operações relacionadas com a Azure Machine Learning que podem ser criadas no registo de Atividades.

| Operação | Descrição |
|:---|:---|
| Cria ou atualiza um espaço de trabalho de Machine Learning | Um espaço de trabalho foi criado ou atualizado |
| CheckComputeNameAvailability | Verifique se um nome de computação já está em uso |
| Cria ou atualiza os recursos computacional | Um recurso compute foi criado ou atualizado |
| Elimina os recursos computacional | Um recurso compute foi eliminado |
| Listar segredos | Na operação listado segredos para um espaço de trabalho machine learning |

## <a name="resource-logs"></a>Registos do recurso

Esta secção lista os tipos de registos de recursos que pode recolher para o espaço de trabalho Azure Machine Learning.

Fornecedor e Tipo de Recursos: [Microsoft.MachineLearningServices/workspace](/azure/azure-monitor/platform/resource-logs-categories#microsoftmachinelearningservicesworkspaces).

| Categoria | Nome a Apresentar |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Esquemas

Os seguintes esquemas estão em uso pela Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Propriedade | Descrição |
|:--- |:---|
| TimeGenerated | Tempo em que a entrada de registo foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento log, AmlComputeClusterNodeEvent |
| JobId | ID do Trabalho submetido |
| Experimentid | ID da Experiência |
| Nome de experiência | Nome da Experiência |
| CustomerSubscriptionId | Subscrição Onde Experimente e Job como submetidos |
| Espaço de trabalhoName | Nome do espaço de trabalho de aprendizagem automática |
| ClusterName | Nome do Cluster |
| Estado de Provisionamento | Estado da submissão do Emprego |
| ResourceGroupName | Nome do grupo de recursos |
| Nome de emprego | Nome do Trabalho |
| ClusterId | ID do cluster |
| EventType | Tipo de evento job. Por exemplo, JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Estado do trabalho (a Corrida). Por exemplo, Queued, Running, Succeeded, Failed |
| ErrorDetails | Detalhes do erro de trabalho |
| CriaçãoApiVersão | Versão Api usada para criar o trabalho |
| ClusterResourceGroupName | Nome do grupo de recursos do cluster |
| TFWorkerCount | Conde dos trabalhadores da TF |
| TFParameterServerCount | Conde do servidor de parâmetros TF |
| Tipo de Ferramentas | Tipo de ferramenta utilizada |
| RunInContainer | Bandeira descrevendo se o trabalho deve ser executado dentro de um recipiente |
| JobErrorMessage | mensagem detalhada de erro de trabalho |
| NodeId | ID do nó criado onde o trabalho está em execução |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Tempo em que a entrada de registo foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento log, AmlComputeClusterNodeEvent |
| Estado de Provisionamento | Estado de provisionamento do cluster |
| ClusterName | Nome do cluster |
| ClusterType | Tipo do cluster |
| CreatedBy | Utilizador que criou o cluster |
| CoreCount | Contagem dos núcleos no aglomerado |
| VmSize | Tamanho Vm do cluster |
| VmPriority | Prioridade dos nós criados dentro de um cluster Dedicado/LowPriority |
| Escalatype | Tipo de conjunto de ampliação manual/automóvel |
| IniciaisNodeCount | Contagem inicial do nó do cluster |
| MínimoNodeCount | Contagem mínima de nó do cluster |
| MáximoNodeCount | Contagem máxima do nó do cluster |
| NodeDeallocationOption | Como o nó deve ser transatado |
| Publisher | Editor do tipo de cluster |
| Oferta | Oferta com a qual o cluster é criado |
| Sku | Sku do nó/VM criado dentro do cluster |
| Versão | Versão da imagem utilizada enquanto o nó/VM é criado |
| SubnetId | Subnetida do cluster |
| Estado de Atribuição | Estado de atribuição de clusters |
| CurrentNodeCount | Contagem atual do nó do cluster |
| TargetNodeCount | Contagem de nós de destino do cluster enquanto escala para cima/para baixo |
| EventType | Tipo de evento durante a criação de cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo de marcha lenta em segundos antes que o cluster seja reduzido |
| PreemptedNodeCount | Contagem de nódoaista preempted do cluster |
| IsResizeGrow | Bandeira indicando que o cluster está a aumentar |
| VmFamilyName | Nome da família VM dos nómadas que podem ser criados dentro do cluster |
| LeavingNodeCount | Deixando a contagem de nó do cluster |
| Não utilizávelNodeCount | Contagem de nó inutilizável do cluster |
| IdleNodeCount | Contagem de nó ocioso do cluster |
| RunningNodeCount | Contagem de nó de execução do cluster |
| Preparação NodeCount | Preparação da contagem de nó do cluster |
| QuotaAllotada | Quota atribuída ao cluster |
| QuotaUtilizado | Quota utilizada do cluster |
| Tempo de Transferência do Estado de Atribuição | Tempo de transição de um estado para outro |
| ClusterErrorCodes | Código de erro recebido durante a criação ou dimensionamento do cluster |
| CriaçãoApiVersão | Versão Api usada ao criar o cluster |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents tabela

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Tempo em que a entrada de registo foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento log, AmlComputeClusterNodeEvent |
| ClusterName | Nome do cluster |
| NodeId | ID do nó de cluster criado |
| VmSize | Tamanho Vm do nó |
| VmFamilyName | Família Vm a que o nó pertence |
| VmPriority | Prioridade do nó criado Dedicado/LowPriority |
| Publisher | Editor da imagem vm. Por exemplo, microsoft-dsvm |
| Oferta | Oferta associada à criação de VM |
| Sku | Sku do nó/VM criado |
| Versão | Versão da imagem utilizada enquanto o nó/VM é criado |
| ClusterCreationTime | Tempo em que o cluster foi criado |
| ResizeStartTime | Tempo em que a escala de cluster para cima/para baixo começou |
| RedimensionarEndTime | Tempo em que a escala de cluster para cima/para baixo terminou |
| Horário de NodeAllocation | Tempo em que nó foi atribuído |
| NodeBootTime | Tempo em que nó foi arrancado |
| StartTaskStartTime | Tempo em que a tarefa foi atribuída a um nó e começou |
| StartTaskEndTime | Tempo quando a tarefa atribuída a um nó terminou |
| TotalE2ETimeInSegundos | O nó de tempo total estava ativo |


## <a name="see-also"></a>Ver também

- Consulte [a monitorização da aprendizagem automática Azure](monitor-azure-machine-learning.md) para uma descrição da monitorização da aprendizagem automática do Azure.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) para obter informações sobre a monitorização dos recursos do Azure.
