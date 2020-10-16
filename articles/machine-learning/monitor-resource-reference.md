---
title: Referência de dados de monitorização / Microsoft Docs
titleSuffix: Azure Machine Learning
description: Conheça os dados e recursos recolhidos para a Azure Machine Learning e disponível no Azure Monitor. O Azure Monitor recolhe e sobrea parece dados sobre o seu espaço de trabalho de aprendizagem de máquinas Azure, e permite-lhe visualizar métricas, definir alertas e analisar dados registados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: 405b0aa051d0d1142d7dd4ccbf2bca4ef9cc3545
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650597"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Referência de dados de monitorização de machine learning Azure

Conheça os dados e recursos recolhidos pelo Azure Monitor a partir do seu espaço de trabalho Azure Machine Learning. Consulte [a Monitorização da Aprendizagem automática Azure](monitor-azure-machine-learning.md) para obter detalhes sobre a recolha e análise de dados de monitorização.

## <a name="resource-logs"></a>Registos do recurso

A tabela que se segue lista as propriedades dos registos de recursos Azure Machine Learning quando são recolhidos em Registos monitores Azure ou Azure Storage.

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
| EventType | Tipo de evento Job, por exemplo, JobSubmitted, JobRunning, JobFailed, JobSucceed, etc. |
| ExecutionState | Estado do trabalho (a corrida), por exemplo, Queued, Running, Succeeded, Failed |
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
| Publisher | Editor da imagem vm, por exemplo, microsoft-dsvm |
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

### <a name="metrics"></a>Métricas

As tabelas que se seguem listam as métricas da plataforma recolhidas para a Azure Machine Learning Todas as métricas são armazenadas no espaço de trabalho **de aprendizagem da máquina Azure.**

**Modelo**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| A implementação do modelo falhou | Contagem | O número de implementações de modelos que falharam. |
| Implementação de modelos iniciado | Contagem | O número de implementações de modelos começou. |
| Implementação de modelos conseguiu | Contagem | O número de implementações de modelos que conseguiram. |
| Registo modelo falhou | Contagem | O número de registos de modelos que falharam. |
| Registo modelo conseguiu | Contagem | O número de registos de modelos que se sucederam. |

**Quota**

A informação sobre quotas é apenas para o cálculo Azure Machine Learning.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Núcleos ativos | Contagem | O número de núcleos de computação ativo. |
| Nódes ativos | Contagem | O número de nós ativos. |
| Núcleos ociosos | Contagem | O número de núcleos de computação ocioso. |
| Nódoas ociosas | Contagem | O número de nós de computação ocioso. |
| Deixando núcleos | Contagem | O número de núcleos de saída. |
| Deixando os nódoas | Contagem | O número de nós de saída. |
| Núcleos preemptidos | Contagem | O número de núcleos preempted. |
| Nódoas presas | Contagem | O número de nós preempted. |
| Percentagem de utilização de quotas | Percentagem | A percentagem de quota utilizada. |
| Total de núcleos | Contagem | Os núcleos totais. |
| Nódoas totais | Contagem | Os nós totais. |
| Núcleos inutilizáveis | Contagem | O número de núcleos inutilizáveis. |
| Nódes inutilizáveis | Contagem | O número de nós inutilizáveis. |

Seguem-se as dimensões que podem ser utilizadas para filtrar métricas de quota:

| Dimensão | Métrica(s) disponível com | Descrição |
| ---- | ---- | ---- |
| Nome do Cluster | Todas as métricas de quota | O nome da instância computacional. |
| Nome da família VM | Percentagem de utilização de quotas | O nome da família VM usado pelo aglomerado. |
| Prioridade Vm | Percentagem de utilização de quotas | A prioridade do VM.

**Recurso**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| CpuUtilização | Percentagem | Quanto por cento do CPU foi usado para um dado nó durante uma corrida/trabalho. Esta métrica só é publicada quando um trabalho está a funcionar num nó. Um trabalho pode usar um ou mais nós. Esta métrica é publicada por nó. |
| GpuUtilização | Percentagem | Quanto percentagem de GPU foi utilizada para um dado nó durante uma corrida/trabalho. Um nó pode ter uma ou mais GPUs. Esta métrica é publicada por GPU por nó. |

Seguem-se as dimensões que podem ser utilizadas para filtrar métricas de recursos:

| Dimensão | Descrição |
| ----- | ----- |
| CreatedTime | |
| DeviceId | ID do dispositivo (GPU). Disponível apenas para gpuutilização. |
| NodeId | Identificação do nó criado onde o trabalho está a funcionar. |
| RunId | Identificação da corrida/trabalho. |

**Correr**

Informação sobre treinos.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Execuções concluídas | Contagem | O número de execuções completas. |
| Corridas falhadas | Contagem | O número de corridas falhadas. |
| Começou a correr | Contagem | O número de corridas iniciadas. |

Seguem-se as dimensões que podem ser utilizadas para filtrar métricas de execução:

| Dimensão | Descrição |
| ---- | ---- |
| ComputeType | O tipo de cálculo que a corrida usou. |
| PipelineStepType | O tipo de [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py&preserve-view=true) usado na corrida. |
| PublishedPipelineId | A identificação do oleoduto publicado usado na corrida. |
| RunType | O tipo de corrida. |

Os valores válidos para a dimensão RunType são:

| Valor | Descrição |
| ----- | ----- |
| Experimentação | Não oleoduto funciona. |
| PipelineRun | Um oleoduto, que é o pai de um StepRun. |
| StepRun | Uma corrida para um passo de oleoduto. |
| ReusedStepRun | Uma corrida para um passo de oleoduto que reutiliza uma corrida anterior. |

## <a name="see-also"></a>Consulte também

- Consulte [a monitorização da aprendizagem automática Azure](monitor-azure-machine-learning.md) para uma descrição da monitorização da aprendizagem automática do Azure.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) para obter informações sobre a monitorização dos recursos do Azure.
