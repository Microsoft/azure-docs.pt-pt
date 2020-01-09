---
title: Referência de dados de monitoramento | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os dados e os recursos coletados para Azure Machine Learning e disponíveis em Azure Monitor. Azure Monitor coleta e superfícies de dados sobre seu espaço de trabalho do Azure Machine Learning e permite que você exiba as métricas, defina alertas e analise os dados registrados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/06/2019
ms.openlocfilehash: adfcd79d209f98b664f18b89ceea65bdc84f36dc
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541494"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Referência de dados de monitoramento do Azure Machine Learning

Saiba mais sobre os dados e recursos coletados por Azure Monitor do seu espaço de trabalho Azure Machine Learning. Consulte [monitoramento Azure Machine Learning](service/monitor-azure-machine-learning.md) para obter detalhes sobre como coletar e analisar dados de monitoramento.

## <a name="resource-logs"></a>Registos do recurso

A tabela a seguir lista as propriedades de Azure Machine Learning logs de recursos quando eles são coletados nos logs de Azure Monitor ou no armazenamento do Azure.

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Propriedade | Descrição |
|:--- |:---|
| TimeGenerated | Hora em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| JobId | ID do trabalho enviado |
| Experimentoid | ID do experimento |
| Experimentoname | Nome do experimento |
| CustomerSubscriptionId | SubscriptionId em que o teste e o trabalho são enviados |
| WorkspaceName | Nome do espaço de trabalho do Machine Learning |
| ClusterName | Nome do cluster |
| ProvisioningState | Estado do envio do trabalho |
| ResourceGroupName | Nome do grupo de recursos |
| JobName | Nome do trabalho |
| ClusterId | ID do cluster |
| EventType | Tipo do evento de trabalho, por exemplo, JobSubmitted, JobRunning, JobFailed, JobSucceeded, etc. |
| ExecutionState | Estado do trabalho (a execução), por exemplo, em fila, em execução, com êxito, com falha |
| ErrorDetails | Detalhes do erro de trabalho |
| CreationApiVersion | Versão da API usada para criar o trabalho |
| ClusterResourceGroupName | Nome do grupo de recursos do cluster |
| TFWorkerCount | Contagem de trabalhos de TF |
| TFParameterServerCount | Contagem do servidor de parâmetros TF |
| Ferramenta de | Tipo de ferramenta usada |
| RunInContainer | Sinalizador que descreve se o trabalho deve ser executado dentro de um contêiner |
| JobErrorMessage | mensagem detalhada de erro de trabalho |
| NodeId | ID do nó criado onde o trabalho está em execução |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Hora em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| ProvisioningState | Estado de provisionamento do cluster |
| ClusterName | Nome do cluster |
| ClusterType | Tipo do cluster |
| CreatedBy | Usuário que criou o cluster |
| CoreCount | Contagem dos núcleos no cluster |
| VmSize | Tamanho da VM do cluster |
| VmPriority | Prioridade dos nós criados dentro de um cluster dedicado/LowPriority |
| Dimensionartype | Tipo de dimensionamento manual/automático do cluster |
| InitialNodeCount | Contagem de nós inicial do cluster |
| MinimumNodeCount | Contagem mínima de nós do cluster |
| MaximumNodeCount | Contagem máxima de nós do cluster |
| NodeDeallocationOption | Como o nó deve ser desalocado |
| Publicador | Editor do tipo de cluster |
| Oferta | Oferta com a qual o cluster é criado |
| SKU | SKU do nó/VM criado dentro do cluster |
| Versão | Versão da imagem usada enquanto o nó/VM é criado |
| SubnetId | Sub-redeid do cluster |
| AllocationState | Estado de alocação do cluster |
| CurrentNodeCount | Contagem de nós atual do cluster |
| TargetNodeCount | Contagem de nós de destino do cluster ao escalar/reduzir verticalmente |
| EventType | Tipo de evento durante a criação do cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo ocioso em segundos antes que o cluster seja reduzido verticalmente |
| PreemptedNodeCount | Contagem de nós admitida preempção do cluster |
| IsResizeGrow | Sinalizador que indica que o cluster está aumentando verticalmente |
| VmFamilyName | Nome da família de VMs dos nós que podem ser criados dentro do cluster |
| LeavingNodeCount | Saindo da contagem de nós do cluster |
| UnusableNodeCount | Contagem de nós inutilizáveis do cluster |
| IdleNodeCount | Contagem de nós ociosos do cluster |
| RunningNodeCount | Contagem de nós em execução do cluster |
| PreparingNodeCount | Preparando a contagem de nós do cluster |
| QuotaAllocated | Cota alocada para o cluster |
| QuotaUtilized | Cota utilizada do cluster |
| AllocationStateTransitionTime | Tempo de transição de um estado para outro |
| ClusterErrorCodes | Código de erro recebido durante a criação ou o dimensionamento do cluster |
| CreationApiVersion | Versão da API usada ao criar o cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Hora em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| ClusterName | Nome do cluster |
| NodeId | ID do nó de cluster criado |
| VmSize | Tamanho da VM do nó |
| VmFamilyName | Família de VMs à qual o nó pertence |
| VmPriority | Prioridade do nó criado dedicado/LowPriority |
| Publicador | Editor da imagem da VM, por exemplo, Microsoft-dsvm |
| Oferta | Oferta associada à criação da VM |
| SKU | SKU do nó/VM criado |
| Versão | Versão da imagem usada enquanto o nó/VM é criado |
| ClusterCreationTime | Hora em que o cluster foi criado |
| ResizeStartTime | Hora em que o cluster foi iniciado verticalmente |
| ResizeEndTime | Tempo durante o aumento/redução do cluster concluído |
| NodeAllocationTime | Hora em que o nó foi alocado |
| NodeBootTime | Hora em que o nó foi inicializado |
| StartTaskStartTime | Hora em que a tarefa foi atribuída a um nó e iniciada |
| StartTaskEndTime | Hora em que a tarefa atribuída a um nó terminou |
| TotalE2ETimeInSeconds | O nó de tempo total estava ativo |

### <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas de plataforma coletadas para Azure Machine Learning todas as métricas são armazenadas no namespace **Workspace do Azure Machine Learning**.

**Modelo**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Falha na implantação do modelo | Contagem | O número de implantações de modelo que falharam. |
| Implantação de modelo iniciada | Contagem | O número de implantações de modelo iniciadas. |
| Implantação de modelo bem-sucedida | Contagem | O número de implantações de modelo que tiveram êxito. |
| Falha no registro do modelo | Contagem | O número de registros de modelo que falharam. |
| Registro de modelo bem-sucedido | Contagem | O número de registros de modelo que foram bem-sucedidos. |

**Quota**

As informações de cota são apenas para Azure Machine Learning computação.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Núcleos ativos | Contagem | O número de núcleos de computação ativos. |
| Nós ativos | Contagem | O número de nós ativos. |
| Núcleos ociosos | Contagem | O número de núcleos de computação ociosos. |
| Nós ociosos | Contagem | O número de nós de computação ociosos. |
| Deixando núcleos | Contagem | O número de cores de saída. |
| Saindo de nós | Contagem | O número de nós de saída. |
| Núcleos preempçãos | Contagem | O número de núcleos admitidos. |
| Nós admitidos | Contagem | O número de nós admitidos. |
| Porcentagem de utilização de cota | Percentagem | A porcentagem da cota usada. |
| Total de núcleos | Contagem | O total de núcleos. |
| Total de nós | Contagem | O total de nós. |
| Núcleos inutilizáveis | Contagem | O número de núcleos inutilizáveis. |
| Nós inutilizáveis | Contagem | O número de nós inutilizáveis. |

Veja a seguir as dimensões que podem ser usadas para filtrar as métricas de cota:

| Dimensão | Métricas disponíveis com | Descrição |
| ---- | ---- | ---- |
| Nome do cluster | Todas as métricas de cota | O nome da instância de computação. |
| Nome da família de VMs | Porcentagem de utilização de cota | O nome da família de VMs usada pelo cluster. |
| Prioridade da VM | Porcentagem de utilização de cota | A prioridade da VM.

**Executar**

Informações sobre execuções de treinamento.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Execuções concluídas | Contagem | O número de execuções concluídas. |
| Execuções com falha | Contagem | O número de execuções com falha. |
| Execuções iniciadas | Contagem | O número de execuções iniciadas. |

Veja a seguir as dimensões que podem ser usadas para filtrar as métricas de execução:

| Dimensão | Descrição |
| ---- | ---- |
| Computatype | O tipo de computação usado pela execução. |
| PipelineStepType | O tipo de [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) usado na execução. |
| PublishedPipelineId | A ID do pipeline publicado usado na execução. |
| RunType | O tipo de execução. |

Os valores válidos para a dimensão RunType são:

| Valor | Descrição |
| ----- | ----- |
| Experimentação | Execuções sem pipeline. |
| PipelineRun | Uma execução de pipeline, que é o pai de um StepRun. |
| StepRun | Uma execução para uma etapa de pipeline. |
| ReusedStepRun | Uma execução para uma etapa de pipeline que reutiliza uma execução anterior. |

## <a name="see-also"></a>Veja também

- Consulte [monitoramento Azure Machine Learning](service/monitor-azure-machine-learning.md) para obter uma descrição do Azure Machine Learning de monitoramento.
- Consulte [monitorando recursos do Azure com Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) para obter detalhes sobre como monitorar recursos do Azure.
