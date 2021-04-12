---
title: Monitor Azure Machine Learning referência de dados | Microsoft Docs
description: Documentação de referência para monitorização da Aprendizagem automática Azure. Conheça os dados & recursos recolhidos e disponíveis no Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031074"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitorização da referência de dados de aprendizagem de máquinas Azure

Conheça os dados e recursos recolhidos pelo Azure Monitor a partir do seu espaço de trabalho Azure Machine Learning. Consulte [a Monitorização da Aprendizagem automática Azure](monitor-azure-machine-learning.md) para obter detalhes sobre a recolha e análise de dados de monitorização.

## <a name="metrics"></a>Métricas

Esta secção lista todas as métricas da plataforma recolhidas automaticamente para a Azure Machine Learning. O fornecedor de recursos para estas métricas é [o Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modelação**

| Metric | Unidade | Description |
|--|--|--|
| Registo modelo Bem sucedido | de palavras | Número de registos de modelos que sucederam neste espaço de trabalho |
| Registo modelo falhado | de palavras | Número de registos de modelos que falharam neste espaço de trabalho |
| Implementação de modelos iniciado | de palavras | Número de implementações de modelos iniciadas neste espaço de trabalho |
| Implementação de modelos conseguiu | de palavras | Número de implementações de modelos que foram bem sucedidas neste espaço de trabalho |
| Implementação de modelo falhou | de palavras | Número de implementações de modelos que falharam neste espaço de trabalho |

**Quota**

A informação sobre quotas é apenas para o cálculo Azure Machine Learning.

| Metric | Unidade | Description |
|--|--|--|
| Nómada Total | de palavras | Número de nós totais. Este total inclui alguns dos nóns ativos, nóns ociosos, nóns inutilizáveis, nóns preempted, leaving nodes |
| Nóns ativos | de palavras | Número de nós ativos. Os nós que estão ativamente a gerir um trabalho. |
| Nódoas Ociosas | de palavras | Número de nós ociosos. Os nós inativos são os nós que não estão a gerir nenhum emprego, mas que podem aceitar um novo emprego se estiverem disponíveis. |
| Nódes Inutilizáveis | de palavras | Número de nós inutilizáveis. Os nós inutilizáveis não estão funcionais devido a algum problema irresolúvel. Azure vai reciclar estes nós. |
| Nómadas Preempted | de palavras | Número de nós preempted. Estes nós são os nós de baixa prioridade que são retirados da piscina de nós disponíveis. |
| Deixando os nóns | de palavras | Número de nós de saída. Deixar os nós são os nós que acabaram de processar um trabalho e irão para o estado de Idle. |
| Núcleos Totais | de palavras | Número de núcleos totais |
| Núcleos Ativos | de palavras | Número de núcleos ativos |
| Núcleos Ociosos | de palavras | Número de núcleos ociosos |
| Núcleos inutilizáveis | de palavras | Número de núcleos inutilizáveis |
| Núcleos Preempted | de palavras | Número de núcleos pré-apropriados |
| Deixando os Núcleos | de palavras | Número de núcleos de saída |
| Percentagem de Utilização de Cots | de palavras | Por cento das quotas utilizadas |

**Recurso**

| Metric| Unidade | Description |
|--|--|--|
| CpuUtilização | de palavras | Percentagem de utilização num nó CPU. A utilização é reportada em intervalos de um minuto. |
| GpuUtilização | de palavras | Percentagem de utilização num nó GPU. A utilização é reportada em intervalos de um minuto. |
| GpuMemoryUtilization | de palavras | Percentagem de utilização da memória num nó GPU. A utilização é reportada em intervalos de um minuto. |
| GpuEnergyJoules | de palavras | Energia de intervalo em Joules num nó GPU. A energia é reportada em intervalos de um minuto. |

**Executar**

A informação sobre o treino corre para o espaço de trabalho.

| Metric | Unidade | Description |
|--|--|--|
| Corridas Canceladas | de palavras | Número de corridas canceladas para este espaço de trabalho. O conde é atualizado quando uma execução é cancelada com sucesso. |
| Cancelar execuções solicitadas | de palavras | Número de execuções onde foi solicitado o cancelamento para este espaço de trabalho. A contagem é atualizada quando o pedido de cancelamento foi recebido para uma execução. |
| Execuções Concluídas | de palavras | Número de runs concluídas com sucesso para este espaço de trabalho. A contagem é atualizada quando uma execução tiver sido concluída e a saída tiver sido recolhida. |
| Corridas falhadas | de palavras | Número de corridas falhou neste espaço de trabalho. O conde é atualizado quando uma corrida falha. |
| Finalização de Corridas | de palavras | Número de corridas inseridas no estado finalizado para este espaço de trabalho. A contagem é atualizada quando uma execução tiver terminado, mas a recolha de saída ainda em andamento. | 
| Não respondendo corre | de palavras | Número de corridas que não respondem a este espaço de trabalho. A contagem é atualizada quando uma execução entra no estado de Não Responder. |
| Não começou corre | de palavras | Número de runs em Estado Não Iniciado para este espaço de trabalho. A contagem é atualizada quando um pedido é recebido para criar uma execução, mas a informação de execução ainda não foi povoada. |
| Preparação de Corridas | de palavras | Número de corridas que se preparam para este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de preparação enquanto o ambiente de funcionação está a ser preparado. |
| Corridas de Provisionamento | de palavras | Número de execuções que estão a provisão para este espaço de trabalho. A contagem é atualizada quando uma corrida está à espera da criação ou provisão do alvo do cálculo. |
| Corridas em Fila | de palavras | Número de corridas que estão na fila para este espaço de trabalho. A contagem é atualizada quando uma corrida é em fila no alvo do cálculo. Pode ocorrer quando se espera que os nós computatados necessários estejam prontos. |
| Corridas iniciadas | de palavras | Número de corridas a correr para este espaço de trabalho. A contagem é atualizada quando a execução começa a funcionar com os recursos necessários. |
| Execuções ini 'Starting' | de palavras | Número de corridas iniciadas para este espaço de trabalho. O Conde é atualizado após pedido para criar informações de execução e execução, como o Run ID, foi povoado |
| Erros | de palavras | Número de erros de execução neste espaço de trabalho. A contagem é atualizada sempre que o run encontra um erro. |
| Avisos | de palavras | Número de avisos de execução neste espaço de trabalho. A contagem é atualizada sempre que uma corrida encontra um aviso. |

## <a name="metric-dimensions"></a>Dimensões métricas

Para obter mais informações sobre as dimensões métricas, consulte [métricas multidimensionais.](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

A Azure Machine Learning tem as seguintes dimensões associadas às suas métricas.

| Dimensão | Description |
| ---- | ---- |
| Nome do Cluster | O nome do recurso de cluster compute. Disponível para todas as métricas de quota. |
| Nome da família VM | O nome da família VM usado pelo aglomerado. Disponível para percentagem de utilização de cots. |
| Prioridade Vm | A prioridade do VM. Disponível para percentagem de utilização de cots.
| CreatedTime | Disponível apenas para CpuUtilization e GpuUtilization. |
| DeviceId | ID do dispositivo (GPU). Disponível apenas para gpuutilização. |
| NodeId | Identificação do nó criado onde o trabalho está a funcionar. Disponível apenas para CpuUtilization e GpuUtilization. |
| RunId | Identificação da corrida/trabalho. Disponível apenas para CpuUtilization e GpuUtilization. |
| ComputeType | O tipo de cálculo que a corrida usou. Apenas disponível para execuções concluídas, corridas falhadas e execuções iniciadas. |
| PipelineStepType | O tipo de [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) usado na corrida. Apenas disponível para execuções concluídas, corridas falhadas e execuções iniciadas. |
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

| Operação | Description |
|:---|:---|
| Cria ou atualiza um espaço de trabalho de Machine Learning | Um espaço de trabalho foi criado ou atualizado |
| CheckComputeNameAvailability | Verifique se um nome de computação já está em uso |
| Cria ou atualiza os recursos computacional | Um recurso compute foi criado ou atualizado |
| Elimina os recursos computacional | Um recurso compute foi eliminado |
| Listar segredos | Na operação listado segredos para um espaço de trabalho machine learning |

## <a name="resource-logs"></a>Registos do recurso

Esta secção lista os tipos de registos de recursos que pode recolher para o espaço de trabalho Azure Machine Learning.

Fornecedor e Tipo de Recursos: [Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

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
- Consulte [os recursos de Monitor Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.
