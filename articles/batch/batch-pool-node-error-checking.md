---
title: Verifique se existem erros de agrupamento e o node - Azure Batch
description: Erros para verificar a existência e como evitá-los durante a criação de conjuntos e nós
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 8d8df9935e935ac8d5a1194cfab103a006cf5546
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791346"
---
# <a name="check-for-pool-and-node-errors"></a>Verifique se existem erros de agrupamento e o nó

Ao criar e gerir conjuntos do Azure Batch, algumas operações imediata. No entanto, algumas operações são assíncronas e execução em segundo plano. Eles poderão demorar vários minutos a concluir.

Detecção de falhas para operações que têm lugar imediatamente é simples porque as falhas são retornadas imediatamente a API, CLI ou da interface do Usuário.

Este artigo aborda as operações de segundo plano que podem ocorrer para conjuntos e nós de conjunto. Especifica como pode detectar e evitar falhas.

## <a name="pool-errors"></a>Erros de conjunto

### <a name="resize-timeout-or-failure"></a>Redimensionar o tempo limite ou de falha

Quando o agrupamento de criar um novo conjunto ou redimensionar uma existente, especificar o número de destino de nós.  A operação é concluída imediatamente, mas a alocação real dos novos nós ou a remoção de nós existentes pode demorar alguns minutos.  Especifica o tempo limite de redimensionamento no [crie](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API. Se o Batch não é possível obter o número de destino de nós durante o período de tempo limite de redimensionamento, para a operação. O conjunto entra num estado estável e relatórios de erros de redimensionar.

O [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) relatórios um tempo limite de redimensionamento de propriedade para a avaliação mais recente e apresenta uma lista de todos os erros que ocorreram.

Causas comuns para tempos limite de redimensionamento incluem:

- Tempo limite de redimensionamento é demasiado curta
  - Na maioria das circunstâncias, o tempo limite predefinido de 15 minutos é suficiente para que nós de conjunto ser alocado ou removido.
  - Se alocar um grande número de nós, recomendamos definir o tempo de limite de redimensionamento e 30 minutos. Por exemplo, quando estamos redimensionando a mais de 1000 nós partir de uma imagem do Azure Marketplace ou a mais de 300 nós a partir de uma imagem VM personalizada.
- Quota de núcleos suficiente
  - Uma conta do Batch é limitada no número de núcleos que ele pode alocar em todos os agrupamentos. Batch para alocar nós assim que esse quota foi atingido. [Pode aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) a quota de núcleos, de modo que Batch pode alocar mais nós.
- IPs de sub-rede insuficiente quando um [conjunto estiver numa rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma sub-rede de rede virtual tem de ter suficiente endereços IP para atribuir a cada nó no conjunto pedida não atribuídos. Caso contrário, não não possível criar os nós.
- Recursos insuficientes quando um [conjunto estiver numa rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Pode criar recursos, tais como grupos de segurança de rede, IPs públicos e Balanceadores de carga na mesma subscrição que a conta do Batch. Verifique que as quotas de subscrição são suficientes para estes recursos.
- Agrupamentos de grandes dimensões com imagens de VM personalizadas
  - Grandes pools que utilizam imagens de VM personalizadas, podem demorar mais tempo alocar e redimensionar tempos limite pode ocorrer.  Ver [utilizar uma imagem personalizada para criar um conjunto de máquinas virtuais](https://docs.microsoft.com/azure/batch/batch-custom-images) para recomendações sobre limites e configuração.

### <a name="automatic-scaling-failures"></a>Falhas de dimensionamento automáticas

Também pode definir o Azure Batch para dimensionar automaticamente o número de nós num conjunto. Definir os parâmetros para o [fórmula para um conjunto de dimensionamento automático](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). O serviço Batch utiliza a fórmula para periodicamente avaliar o número de nós no conjunto e definir um novo número de destino. Os seguintes tipos de problemas podem ocorrer:

- A avaliação de dimensionamento automática falha.
- A operação de redimensionamento resultante falha e exceder o tempo limite.
- Um problema com a fórmula de dimensionamento automático nos leva a valores de destino de nó incorreto. O redimensionamento funciona ou exceder o tempo limite.

Pode obter informações sobre a última avaliação de dimensionamento automática com o [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) propriedade. Esta propriedade reporta o tempo de avaliação, os valores e resultado e quaisquer erros de desempenho.

O [evento de conclusão de redimensionamento de conjunto](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) captura informações sobre todas as avaliações.

### <a name="delete"></a>Eliminar

Quando elimina um conjunto que contém nós, o primeiro Batch elimina os nós. Em seguida, elimina o próprio objeto de agrupamento. Pode demorar alguns minutos para que os nós do conjunto ser eliminado.

Os conjuntos do batch a [estado do agrupamento](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) para **eliminar** durante o processo de eliminação. O aplicativo de chamada pode detetar se a eliminação de conjunto demorar demasiado tempo, utilizando o **estado** e **stateTransitionTime** propriedades.

## <a name="pool-compute-node-errors"></a>Erros de nó de computação do conjunto

Mesmo quando o Batch aloca com êxito nós num conjunto, podem causar vários problemas alguns de nós para ser danificados e inutilizáveis. Estes nós incorrer em custos. É importante detetar problemas, para que não está pagando para nós inutilizáveis.

### <a name="start-task-failure"></a>Falha de tarefa de início

Talvez queira especificar opcional [iniciar tarefa](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) para um conjunto. Tal como acontece com qualquer tarefa, pode utilizar ficheiros de recursos e uma linha de comando para transferir a partir do armazenamento. A tarefa de início é executada para cada nó, depois de ser iniciada. O **waitForSuccess** propriedade especifica se o Batch aguarda até que a tarefa de início seja concluída com êxito antes de ele agenda quaisquer tarefas para um nó.

E se tiver configurado o nó para aguardar a conclusão da tarefa de início com êxito, mas a início de tarefas falha? Nesse caso, o nó não é utilizável mas ainda incorre em encargos.

Pode detectar falhas de tarefas de inicialização ao utilizar o [resultado](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) propriedades de nível superior [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) propriedades de nós.

Uma tarefa de início com falhas também faz com que o Batch definir o nó [estado](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) ao **starttaskfailed** se seria definido **waitForSuccess** para **verdadeiro**.

Tal como acontece com qualquer tarefa, podem existir muitas causas para a ativação da tarefa de início.  Para resolver problemas, verifique o stdout, stderr e quaisquer ficheiros de registo de tarefa específica ainda mais.

### <a name="application-package-download-failure"></a>Falha na transferência de pacote de aplicação

Pode especificar um ou mais pacotes de aplicações para um conjunto. O batch transfere os ficheiros de pacote especificado para cada nó e uncompresses os ficheiros depois de iniciado o nó, mas antes das tarefas estão agendadas. É comum para utilizar uma linha de comandos da tarefa de início em conjunto com pacotes de aplicações. Por exemplo, para copiar ficheiros para uma localização diferente ou para executar a configuração.

O nó [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) propriedade relatórios uma falha ao transferir e descomprimir um pacote de aplicação. Batch define o estado do nó como **inutilizável**.

### <a name="node-in-unusable-state"></a>Nó de estado não utilizável

O Azure Batch pode definir os [estado do nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) para **inutilizável** por muitos motivos. Com o estado de nó definido como **inutilizável**, tarefas não podem ser agendadas para o nó, mas ele ainda incorre em encargos.

Batch sempre tenta recuperar nós inutilizáveis, mas a recuperação pode ou não ser possível dependendo da causa.

Se o Batch pode determinar a causa, o nó [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) propriedade relatórios-lo.

Exemplos adicionais de causas **inutilizável** nós incluem:

- Uma imagem VM personalizada é inválida. Por exemplo, uma imagem não está corretamente preparado.
- Uma VM for movida devido a uma falha de infraestrutura ou de uma atualização de nível baixo. Batch recupera o nó.

### <a name="node-agent-log-files"></a>Ficheiros de registo do agente de nó

O processo de agente de lote que é executado em cada nó no conjunto pode fornecer os ficheiros de registo que poderão ser útil se precisa de contactar o suporte sobre um problema de nó do conjunto. Ficheiros de registo para um nó pode ser carregado através do portal do Azure, o Explorador do Batch, ou uma [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e guardar os ficheiros de registo. Em seguida, pode eliminar o nó ou o conjunto para reduzir o custo de nós em execução.

## <a name="next-steps"></a>Passos Seguintes

Verifique o que define seu aplicativo para implementar a verificação de erros abrangente, especialmente para operações assíncronas. Pode ser fundamental para detetar e diagnosticar problemas de imediato.
