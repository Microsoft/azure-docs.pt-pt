---
title: Executar cargas de trabalho em VMs de baixa prioridade económicas - Azure Batch | Documentos da Microsoft
description: Saiba como aprovisionar as VMs de baixa prioridade para reduzir o custo de cargas de trabalho do Azure Batch.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 17668470be3e997c215aacc4cc2c32c80de2dd81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776141"
---
# <a name="use-low-priority-vms-with-batch"></a>Utilizar VMs de baixa prioridade com o Batch

O Azure Batch oferece máquinas de virtuais de baixa prioridade (VMs) para reduzir o custo de cargas de trabalho do Batch. VMs de baixa prioridade realizar novos tipos de cargas de trabalho possíveis, permitindo uma grande quantidade de energia a ser utilizado para um custo muito baixo de computação do Batch.
 
VMs de baixa prioridade tirar partido da capacidade excedente no Azure. Quando especificar VMs de baixa prioridade nos conjuntos, o Azure Batch pode utilizar este surplus, quando disponível.
 
O compromisso para a utilização de VMs de baixa prioridade é que essas VMs podem não estar disponíveis para atribuir ou podem ser suplantadas em qualquer altura, dependendo da capacidade disponível. Por esse motivo, as VMs de baixa prioridade são mais adequadas para determinados tipos de cargas de trabalho. Utilize VMs de baixa prioridade para batch e o processamento assíncrono de cargas de trabalho onde o tempo de conclusão do trabalho é flexível e o trabalho é distribuído por número de VMs.
 
VMs de baixa prioridade são oferecidas a um preço reduzido significativamente em comparação com VMs dedicadas. Para detalhes de preços, consulte [preços do Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Casos de utilização para VMs de baixa prioridade

Tendo em conta as características das VMs de baixa prioridade, o que as cargas de trabalho podem e não é possível usá-los? Em geral, cargas de trabalho de processamento de batch são uma boa opção, como tarefas são divididas em várias tarefas paralelas ou há muitos trabalhos que são aumentados horizontalmente e distribuídos por várias VMs.

-   Para maximizar a utilização da capacidade excedente no Azure, as tarefas adequadas podem aumentar horizontalmente.

-   Ocasionalmente, VMs poderão não estar disponíveis ou são impedidas, que resulta em capacidade reduzida para as tarefas e pode levar a interrupções de tarefas e volta executar. Tarefas de, por conseguinte, tem de ser flexíveis no período de tempo que podem tomar para executar.

-   Poderão ser afetadas mais tarefas com tarefas mais longas se interrompido. Se implementam o ponto de verificação para guardar o progresso conforme executam tarefas de execução longa, em seguida, o impacto de interrupção é reduzido. Tarefas com menores tempos de execução tendem a funcionar melhor com VMs de baixa prioridade, uma vez que o impacto de interrupção é muito menos.

-   Trabalhos de MPI de longa execução que utilizam várias VMs não são adequados para utilizar VMs de baixa prioridade, uma vez que uma VM admitiu preempção for pode levar a todo o trabalho de ter de executar novamente.

Alguns exemplos de casos de utilização de processamento de batch bem adequados para utilizar VMs de baixa prioridade são:

-   **Desenvolvimento e teste**: Em particular, se estão a ser desenvolvidas soluções em grande escala, podem ser percebidas economias significativas. Podem trazer benefícios para todos os tipos de teste, mas o teste de carga em grande escala e testes de regressão são excelente utiliza.

-   **Complementar as capacidade a pedido**: VMs de baixa prioridade podem ser utilizadas para complementar VMs regular dedicadas - quando estiverem disponíveis, tarefas, podem aumentar e, portanto, conclua mais rápida de custo mais baixo; Quando não está disponível, a linha de base de VMs dedicadas permanece disponível.

-   **Tempo de execução de tarefa flexível**: Se houver flexibilidade no tempo de tarefas tem de concluir, em seguida, quedas potenciais na capacidade pela podem tolerar; No entanto, com a adição de tarefas de VMs de baixa prioridade frequência de execução de forma mais rápida e um custo mais baixo.

Os conjuntos do batch podem ser configurados para utilizar VMs de baixa prioridade de diversas formas, consoante a flexibilidade em tempo de execução de tarefa:

-   VMs de baixa prioridade apenas podem ser utilizadas num conjunto. Neste caso, o Batch recupera qualquer capacidade admitiu preempção for quando disponível. Esta configuração é a maneira mais econômica para executar tarefas, como VMs de apenas de baixa prioridade são utilizadas.

-   VMs de baixa prioridade podem ser utilizadas em conjunto com uma linha de base fixo de VMs dedicadas. O número fixo de VMs dedicadas que sempre existe alguma capacidade para manter uma tarefa progredir.

-   Pode existir uma combinação dinâmica de VMs dedicadas e de baixa prioridade, para que as VMs de baixa prioridade barato apenas são utilizadas quando estiverem disponíveis, mas as VMs dedicadas com preços completo são aumentadas verticalmente quando necessário. Esta configuração mantém uma quantidade mínima de capacidade disponível para manter as tarefas em curso.

## <a name="batch-support-for-low-priority-vms"></a>Suporte para o batch para VMs de baixa prioridade

O Azure Batch fornece diversos recursos que tornam fácil consumir e se beneficiar de VMs de baixa prioridade:

-   Os conjuntos do batch podem conter VMs dedicadas e VMs de baixa prioridade. O número de cada tipo de VM pode ser especificado quando um pool é criado ou alterado em qualquer altura para um conjunto existente, usando a operação de redimensionamento explícita ou utilizar o dimensionamento automático. Submissão de trabalhos e tarefas pode permanecer inalterado, não importando os tipos VM no conjunto. Também pode configurar um conjunto completamente utilizar VMs de baixa prioridade para executar tarefas de forma económica quanto possível, mas criar VMs dedicadas, se a capacidade diminuir abaixo de um limite mínimo, para manter as tarefas em execução.

-   Os conjuntos do batch procuram automaticamente o número de destino de VMs de baixa prioridade. Se as VMs são impedidas, o Batch tenta substituir a capacidade perdida e voltar para o destino.

-   Quando as tarefas sejam interrompidas, o Batch Deteta e requeues automaticamente tarefas sejam executadas novamente.

-   VMs de baixa prioridade têm uma quota de vCPU separado que difere do que para VMs dedicadas. 
    A quota para VMs de baixa prioridade é maior do que a quota para VMs dedicadas, porque as VMs de baixa prioridade custam menos. Para obter mais informações, consulte [limites e quotas de serviço do Batch](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> VMs de baixa prioridade não são atualmente suportadas para contas do Batch criadas no [modo de subscrição de utilizador](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Criar e atualizar conjuntos

Um conjunto do Batch pode conter VMs dedicadas e baixa prioridade (também referidas como nós de computação). Pode definir o número de destino de nós de computação para VMs dedicadas e baixa prioridade. O número de destino de nós Especifica o número de VMs que deseja ter no conjunto.

Por exemplo, para criar um conjunto utiliza serviço cloud do Azure VMs com um destino de 5 dedicados VMs e 20 VMs de baixa prioridade:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Para criar um conjunto a máquinas virtuais do Azure (em VMs do Linux neste caso) a utilizar com um destino de 5 dedicado VMs e 20 VMs de baixa prioridade:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Pode obter o número atual de nós para VMs dedicadas e baixa prioridade:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Nós de conjunto de ter uma propriedade para indicar se o nó é uma VM dedicada ou de baixa prioridade:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Quando um ou mais nós num conjunto são impedidas, uma lista de operação de nós no conjunto ainda retornará esses nós. O número atual de nós de baixa prioridade permanece inalterado, mas esses nós tem seu estado definido como o **Preempted** estado. Batch tenta encontrar VMs de substituição e, se tiver êxito, os nós de percorrer **Creating** e, em seguida **inicial** Estados antes de se tornar disponível para a execução da tarefa, tal como os novos nós.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Dimensionar um conjunto que contém VMs de baixa prioridade

Como com os conjuntos consiste apenas em VMs dedicadas, é possível dimensionar um conjunto que contém VMs de baixa prioridade, chamando o método de redimensionamento ou ao utilizar o dimensionamento automático.

A operação de redimensionamento de conjunto demora um segundo parâmetro opcional que atualiza o valor de **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A fórmula de dimensionamento automático de agrupamento suporta VMs de baixa prioridade da seguinte forma:

-   Pode obter ou definir o valor da variável definidas pelo serviço **$TargetLowPriorityNodes**.

-   Pode obter o valor da variável definidas pelo serviço **$CurrentLowPriorityNodes**.

-   Pode obter o valor da variável definidas pelo serviço **$PreemptedNodeCount**. 
    Esta variável retorna o número de nós no Estado admitiu preempção for e permite-lhe ampliar ou reduzir verticalmente o número de nós dedicados, dependendo do número de nós admitiu preempção for que não estão disponíveis.

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas

Trabalhos e tarefas exigem pouca configuração adicional para nós de baixa prioridade o suporte a apenas é o seguinte:

-   A propriedade de JobManagerTask de uma tarefa tem uma nova propriedade **AllowLowPriorityNode**. 
    Quando esta propriedade for true, a tarefa de gestão pode ser agendada num nó dedicado ou de baixa prioridade. Se esta propriedade é false, a tarefa de gestão está agendada para apenas um nó dedicado.

-   Uma [variável de ambiente](batch-compute-node-environment-variables.md) está disponível para um aplicativo de tarefas para que ele possa determinar se está em execução num nó de baixa prioridade ou dedicado. A variável de ambiente é AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Preempção de tratamento

As VMs podem ocasionalmente ser suplantadas; Quando preempção acontece, o Batch faz o seguinte:

-   As VMs admitiu preempção for têm o estado atualizado para **Preempted**.
-   Se a tarefas estavam em execução nas VMs de nó admitiu preempção for, em seguida, essas tarefas são recolocadas e execute novamente.
-   A VM é eliminada com eficiência, levando a perda de todos os dados armazenados localmente na VM.
-   O conjunto continuamente tenta contactar o número de destino de nós de prioridade baixa disponíveis. Quando a capacidade de substituição é encontrada, os nós manter suas IDs, mas serão reinicializados percorrer **Creating** e **inicial** Estados antes de estarem disponíveis para o agendamento de tarefas.
-   Contagens de preempção estão disponíveis como uma métrica no portal do Azure.

## <a name="metrics"></a>Métricas

Novas métricas estão disponíveis no [portal do Azure](https://portal.azure.com) para nós de baixa prioridade. Estas métricas são:

- Contagem de nós de baixa prioridade
- Número de núcleos de baixa prioridade 
- Contagem de nós admitiu preempção for

Para ver métricas no portal do Azure:

1. Navegue até à sua conta do Batch no portal e ver as definições para a sua conta do Batch.
2. Selecione **métricas** partir do **monitorização** secção.
3. Selecione as métricas pretendidos ao nível dos **métricas disponíveis** lista.

![Métricas de nós de baixa prioridade](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Passos Seguintes

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
