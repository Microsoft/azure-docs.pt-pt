---
title: Monitorização da referência de dados [ Microsoft Docs
titleSuffix: Azure Machine Learning
description: Conheça os dados e recursos recolhidos para o Azure Machine Learning e disponível no Azure Monitor. O Azure Monitor recolhe e supera dados sobre o seu espaço de trabalho azure machine learning, e permite-lhe visualizar métricas, definir alertas e analisar dados registados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927554"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Referência de dados de monitorização de machine learning azure

Conheça os dados e recursos recolhidos pelo Azure Monitor a partir do seu espaço de trabalho azure Machine Learning. Consulte a Monitorização da [Aprendizagem automática azure](monitor-azure-machine-learning.md) para obter detalhes sobre a recolha e análise de dados de monitorização.

## <a name="resource-logs"></a>Registos do recurso

A tabela seguinte lista as propriedades dos registos de recursos azure machine learning quando são recolhidas em Registos de Monitores Azure ou armazenamento azure.

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Propriedade | Descrição |
|:--- |:---|
| TimeGenerated | Tempo em que a entrada de registo foi gerada |
| OperationName | Nome da operação associada ao evento de registo |
| Categoria | Nome do evento de registo, AmlComputeClusterNodeEvent |
| JobId | ID do Trabalho submetido |
| Experimentóide | ID da Experiência |
| Nome da experiência | Nome da Experiência |
| CustomerSubscriptionId | Subscrição Id onde Experiment and Job como apresentado |
| Nome do espaço de trabalho | Nome do espaço de trabalho de aprendizagem automática |
| ClusterName | Nome do Cluster |
| ProvisioningState | Submissão do Estado do Trabalho |
| ResourceGroupName | Nome do grupo de recursos |
| Nome de emprego | Nome do Trabalho |
| Clusterid | ID do cluster |
| Tipo de evento | Tipo de evento de trabalho, por exemplo, JobSubmitted, JobRunning, JobFailed, JobSucceeded, etc. |
| ExecutionState | Estado do trabalho (a Corrida), por exemplo, Fila, Corrida, Sucesso, Falhado |
| ErrorDetails | Detalhes do erro de trabalho |
| CriaçãoApiVersão | Versão Api usada para criar o trabalho |
| ClusterResourceGroupName | Nome do grupo de recursos do cluster |
| TFWorkerCount | Conde dos trabalhadores da TF |
| TFParameterServerCount | Contagem do servidor de parâmetros TF |
| Tipo de ferramentas | Tipo de ferramenta utilizada |
| Runincontainer | Bandeira descrevendo se o trabalho deve ser executado dentro de um recipiente |
| Mensagem de erro de emprego | mensagem detalhada do erro de trabalho |
| NodeId | ID do nó criado onde o trabalho está correndo |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Tempo em que a entrada de registo foi gerada |
| OperationName | Nome da operação associada ao evento de registo |
| Categoria | Nome do evento de registo, AmlComputeClusterNodeEvent |
| ProvisioningState | Estado de provisionamento do cluster |
| ClusterName | Nome do aglomerado |
| ClusterType | Tipo do cluster |
| Criado por | Utilizador que criou o cluster |
| CoreCount | Conde dos núcleos no aglomerado |
| VmSize | Tamanho Vm do cluster |
| VmPriority | Prioridade dos nós criados dentro de um cluster Dedicado/LowPriority |
| Tipo de escala | Tipo de manual/automóvel de escala de cluster |
| Contagem inicial | Contagem inicial do nó do cluster |
| MínimoSNodeCount | Contagem mínima de nó do cluster |
| Contagem máxima | Contagem máxima do nó do cluster |
| NodeDeallocationOption | Como o nó deve ser lodo |
| Publicador | Editor do tipo cluster |
| Oferta | Oferta com a qual o cluster é criado |
| Sku | Sku do Nó/VM criado dentro do cluster |
| Versão | Versão da imagem utilizada enquanto o Nó/VM é criado |
| SubnetId | SubnetId do cluster |
| Estado de Atribuição | Estado de alocação de cluster |
| Contagem atual | Contagem atual do nó do cluster |
| TargetNodeCount | Contagem de nó do alvo do cluster enquanto escala para cima/para baixo |
| Tipo de evento | Tipo de evento durante a criação de cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo inativo em segundos antes de o aglomerado ser reduzido |
| Contagem de NodeS pré-empreitada | Contagem de nós preempted do cluster |
| IsResizeGrow | Bandeira indicando que o aglomerado está a escalonar |
| Nome VmFamily | Nome da família VM dos nós que podem ser criados dentro do cluster |
| Deixando o Número de Nodede | Deixando a contagem do nó do aglomerado |
| InutilizávelNodeCount | Contagem inutilizável do aglomerado |
| IdleNodeCount | Contagem de nós ocioso do aglomerado |
| RunningNodeCount | Contagem de nós de execução do cluster |
| PrepareNodeCount | Preparando a contagem do nó do cluster |
| QuotaAllocated | Quota atribuída ao cluster |
| QuotaUtilized | Quota utilizada do cluster |
| Tempo de Transição do Estado de Atribuição | Tempo de transição de um estado para outro |
| ClusterErrorCodes | Código de erro recebido durante criação de cluster ou escala |
| CriaçãoApiVersão | Versão Api usada ao criar o cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Tempo em que a entrada de registo foi gerada |
| OperationName | Nome da operação associada ao evento de registo |
| Categoria | Nome do evento de registo, AmlComputeClusterNodeEvent |
| ClusterName | Nome do aglomerado |
| NodeId | ID do nó de cluster criado |
| VmSize | Tamanho Vm do nó |
| Nome VmFamily | Família Vm a que o nó pertence |
| VmPriority | Prioridade do nó criado Dedicado/LowPriority |
| Publicador | Editor da imagem vm, por exemplo, microsoft-dsvm |
| Oferta | Oferta associada à criação de VM |
| Sku | Sku do Nó/VM criado |
| Versão | Versão da imagem utilizada enquanto o Nó/VM é criado |
| Tempo de Criação de Cluster | Tempo em que o cluster foi criado |
| Redimensionar o Tempo de Início | Tempo em que a escala de cluster para cima/para baixo começou |
| Tempo de Redimensionar EndTime | Tempo em que a escala de cluster para cima/para baixo terminou |
| Hora do Nó | Tempo em que nó foi atribuído |
| NodeBootTime | Tempo em que Nó foi arrancado |
| Início da tarefa | Tempo em que a tarefa foi atribuída a um nó e começou |
| Início tasktaskEndTime | Tempo em que a tarefa atribuída a um nó terminou |
| Totale2eTimeInSeconds | Nó total do tempo estava ativo |

### <a name="metrics"></a>Métricas

As tabelas seguintes listam as métricas da plataforma recolhidas para o Azure Machine Learning Todas as métricas são armazenadas no espaço de trabalho de **aprendizagem automática Azure**.

**Modelo**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Implementação do modelo falhou | Contagem | O número de modelas que falharam. |
| Implementação de modelos iniciado | Contagem | O número de implementações de modelos começou. |
| Implementação de modelos conseguiu | Contagem | O número de implementações de modelos que conseguiram. |
| Registo de modelos falhado | Contagem | O número de registos de modelos que falharam. |
| Registo de modelos bem sucedido | Contagem | O número de registos de modelos que foram bem sucedidos. |

**Quota**

A informação sobre quotas é apenas para a computação Azure Machine Learning.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Núcleos ativos | Contagem | O número de núcleos de computação ativa. |
| Nódosos ativos | Contagem | O número de nós ativos. |
| Núcleos ociosos | Contagem | O número de núcleos de computação ociosos. |
| Nódoas ociosas | Contagem | O número de nós de computação ociosa. |
| Deixando núcleos | Contagem | O número de núcleos de saída. |
| Deixando nódosos | Contagem | O número de nós que saem. |
| Núcleos pré-empreitados | Contagem | O número de núcleos pré-empreitados. |
| Nódosos pré-empreitados | Contagem | O número de nós pré-optados. |
| Percentagem de utilização de quotas | Percentagem | A percentagem de quota utilizada. |
| Total de núcleos | Contagem | Os núcleos totais. |
| Nósodinsos totais | Contagem | Os nódosos totais. |
| Núcleos inutilizáveis | Contagem | O número de núcleos inutilizáveis. |
| Nós inutilizáveis | Contagem | O número de nós inutilizáveis. |

Seguem-se dimensões que podem ser utilizadas para filtrar métricas de quota:

| Dimensão | Métricas disponíveis com | Descrição |
| ---- | ---- | ---- |
| Nome do Cluster | Todas as métricas de quota | O nome da instância computacional. |
| Nome da família Vm | Percentagem de utilização de quotas | O nome da família VM usada pelo aglomerado. |
| Prioridade Vm | Percentagem de utilização de quotas | A prioridade do VM.

**Executar**

Informação sobre treinos.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Execuções concluídas | Contagem | O número de execuções concluídas. |
| Corridas falhadas | Contagem | O número de corridas falhadas. |
| Começou a correr | Contagem | O número de corridas iniciadas. |

Seguem-se dimensões que podem ser usadas para filtrar métricas de execução:

| Dimensão | Descrição |
| ---- | ---- |
| ComputeType | O tipo de computação que a execução usou. |
| PipelineStepType | O tipo de [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) utilizado na corrida. |
| PublicadoPipelineid | A identificação do gasoduto publicado usado na corrida. |
| Tipo de execução | O tipo de corrida. |

Os valores válidos para a dimensão RunType são:

| Valor | Descrição |
| ----- | ----- |
| Experimentação | Corridas sem oleodutos. |
| PipelineRun | Uma corrida de gasodutos, que é o pai de um StepRun. |
| StepRun | Uma corrida para um passo de oleoduto. |
| StepRun reutilizado | Uma corrida para um passo de oleoduto que reutiliza uma corrida anterior. |

## <a name="see-also"></a>Veja também

- Consulte a monitorização da [Aprendizagem automática Azure](monitor-azure-machine-learning.md) para obter uma descrição da monitorização do Azure Machine Learning.
- Consulte [a Monitorização dos recursos do Azure com o Monitor Azure](/azure/azure-monitor/insights/monitor-azure-resource) para obter mais informações sobre a monitorização dos recursos do Azure.
