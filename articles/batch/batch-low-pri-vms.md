---
title: Executar cargas de trabalho em VMs de baixa prioridade econômicas-lote do Azure | Microsoft Docs
description: Saiba como provisionar VMs de baixa prioridade para reduzir o custo de cargas de trabalho do lote do Azure.
services: batch
author: mscurrell
manager: gwallace
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 12/05/2019
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 39d332a6d069a4e9fac8545f4d08a986c8984c9b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926293"
---
# <a name="use-low-priority-vms-with-batch"></a>Utilizar VMs de baixa prioridade com o Batch

O lote do Azure oferece VMs (máquinas virtuais) de baixa prioridade para reduzir o custo de cargas de trabalho do lote. As VMs de baixa prioridade tornam os novos tipos de cargas de trabalho do lote possíveis, permitindo que uma grande quantidade de poder de computação seja usada para um custo muito baixo.
 
As VMs de baixa prioridade aproveitam a capacidade excedente no Azure. Quando você especifica VMs de baixa prioridade em seus pools, o lote do Azure pode usar esse excedente, quando disponível.
 
A desvantagem de usar VMs de baixa prioridade é que essas VMs podem não estar disponíveis para serem alocadas ou podem sofrer preempção a qualquer momento, dependendo da capacidade disponível. Por esse motivo, as VMs de baixa prioridade são mais adequadas para determinados tipos de cargas de trabalho. Use VMs de baixa prioridade para cargas de trabalho de processamento assíncrono e em lote, em que o tempo de conclusão de trabalhos é flexível e o trabalho é distribuído entre várias VMs.
 
As VMs de baixa prioridade são oferecidas em um preço significativamente reduzido em comparação com as VMs dedicadas. Para obter detalhes de preços, consulte [preços do lote](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> As [VMs pontuais](https://azure.microsoft.com/pricing/spot/) agora estão disponíveis para [VMs de instância única](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) e [conjuntos de dimensionamento de VM](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot). As VMs pontuais são uma evolução de VMs de baixa prioridade, mas diferentes no preço podem variar e um preço máximo opcional pode ser definido ao alocar VMs Spot.
>
> Os pools do lote do Azure começarão a dar suporte a VMs pontuais no primeiro trimestre de 2020 com novas versões das [APIs e ferramentas do lote](https://docs.microsoft.com/azure/batch/batch-apis-tools). As VMs de baixa prioridade continuarão a ter suporte, usando as versões atuais da API e da ferramenta, por pelo menos 12 meses, para permitir tempo suficiente para a migração identificar as VMs. 
>
> Não haverá suporte para VMs pontuais para pools de [configuração de serviço de nuvem](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) . Para usar VMs pontuais, os pools de serviço de nuvem precisarão ser migrados para os pools de [configuração de máquina virtual](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) .


## <a name="use-cases-for-low-priority-vms"></a>Casos de uso para VMs de baixa prioridade

Dadas as características de VMs de baixa prioridade, quais cargas de trabalho podem e não podem usá-las? Em geral, as cargas de trabalho de processamento em lote são uma boa opção, pois os trabalhos são divididos em muitas tarefas paralelas ou há muitos trabalhos que são dimensionados e distribuídos em várias VMs.

-   Para maximizar o uso da capacidade excedente no Azure, os trabalhos adequados podem ser expandidos.

-   Ocasionalmente, as VMs podem não estar disponíveis ou sofrer preempção, o que resulta em menor capacidade para trabalhos e pode levar à interrupção e à reexecutação da tarefa. Os trabalhos devem, portanto, ser flexíveis no tempo que podem ser executados.

-   Trabalhos com tarefas mais longas podem ser impactados mais se forem interrompidos. Se as tarefas de execução longa implementarem o ponto de verificação para salvar o progresso à medida que elas forem executadas, o impacto da interrupção será reduzido. As tarefas com tempos de execução mais curtos tendem a funcionar melhor com VMs de baixa prioridade, pois o impacto da interrupção é muito menos.

-   Trabalhos MPI de longa execução que utilizam várias VMs não são adequados para usar VMs de baixa prioridade, pois uma VM admitida pode levar a um trabalho inteiro que precisa ser executado novamente.

Alguns exemplos de casos de uso de processamento em lotes bem adequados para usar VMs de baixa prioridade são:

-   **Desenvolvimento e teste**: em particular, se soluções em grande escala estiverem sendo desenvolvidas, poderão ser percebidas economias significativas. Todos os tipos de teste podem se beneficiar, mas testes de carga e teste de regressão em larga escala são ótimos usos.

-   **Complementando a capacidade sob demanda**: VMs de baixa prioridade podem ser usadas para complementar VMs dedicadas regulares – quando disponíveis, os trabalhos podem ser dimensionados e, portanto, concluídos mais rapidamente para um custo menor; Quando não estiver disponível, a linha de base das VMs dedicadas permanecerá disponível.

-   **Tempo de execução de trabalho flexível**: se houver flexibilidade no tempo que os trabalhos precisam ser concluídos, quedas potenciais na capacidade poderão ser toleradas; no entanto, com a adição de trabalhos de VMs de baixa prioridade com frequência são executados mais rapidamente e por um custo mais baixo.

Os pools do lote podem ser configurados para usar VMs de baixa prioridade de algumas maneiras, dependendo da flexibilidade no tempo de execução do trabalho:

-   As VMs de baixa prioridade podem ser usadas somente em um pool. Nesse caso, o lote recupera qualquer capacidade admitida quando disponível. Essa configuração é a maneira mais barata de executar trabalhos, pois apenas VMs de baixa prioridade são usadas.

-   As VMs de baixa prioridade podem ser usadas em conjunto com uma linha de base fixa de VMs dedicadas. O número fixo de VMs dedicadas garante que sempre haja alguma capacidade para manter um trabalho em andamento.

-   Pode haver uma mistura dinâmica de VMs dedicadas e de baixa prioridade, para que as VMs de baixa prioridade mais baratas sejam usadas somente quando disponíveis, mas as VMs dedicadas de preço total são aumentadas quando necessário. Essa configuração mantém uma quantidade mínima de capacidade disponível para manter os trabalhos em andamento.

## <a name="batch-support-for-low-priority-vms"></a>Suporte em lote para VMs de baixa prioridade

O lote do Azure fornece vários recursos que facilitam o consumo e o benefício de VMs de baixa prioridade:

-   Os pools do lote podem conter VMs dedicadas e VMs de baixa prioridade. O número de cada tipo de VM pode ser especificado quando um pool é criado ou alterado a qualquer momento para um pool existente, usando a operação de redimensionamento explícito ou usando o dimensionamento automático. O envio de trabalhos e tarefas pode permanecer inalterado, independentemente dos tipos de VM no pool. Você também pode configurar um pool para usar completamente as VMs de baixa prioridade para executar trabalhos o mais barato possível, mas para acelerar as VMs dedicadas se a capacidade cair abaixo de um limite mínimo, para manter os trabalhos em execução.

-   Os pools do lote buscam automaticamente o número de destino de VMs de baixa prioridade. Se as VMs forem admitidas com preempção, o lote tentará substituir a capacidade perdida e retornar ao destino.

-   Quando as tarefas são interrompidas, o lote detecta e enfileira automaticamente as tarefas para serem executadas novamente.

-   As VMs de baixa prioridade têm uma cota de vCPU separada que difere daquela para VMs dedicadas. 
    A cota para VMs de baixa prioridade é maior do que a cota para VMs dedicadas, pois as VMs de baixa prioridade custam menos. Para obter mais informações, consulte [cotas e limites do serviço de lote](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Atualmente, não há suporte para VMs de baixa prioridade em contas do lote criadas no [modo de assinatura do usuário](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Criar e atualizar pools

Um pool do lote pode conter VMs dedicadas e de baixa prioridade (também conhecidas como nós de computação). Você pode definir o número de destino de nós de computação para VMs dedicadas e de baixa prioridade. O número de nós de destino especifica o número de VMs que você deseja ter no pool.

Por exemplo, para criar um pool usando VMs do serviço de nuvem do Azure com um destino de 5 VMs dedicadas e 20 VMs de baixa prioridade:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Para criar um pool usando máquinas virtuais do Azure (neste caso, VMs do Linux) com um destino de 5 VMs dedicadas e 20 VMs de baixa prioridade:

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

Você pode obter o número atual de nós para VMs dedicadas e de baixa prioridade:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Os nós de pool têm uma propriedade para indicar se o nó é uma VM dedicada ou de baixa prioridade:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Quando um ou mais nós em um pool são preempçãos, uma operação listar nós no pool ainda retorna esses nós. O número atual de nós de baixa prioridade permanece inalterado, mas esses nós têm seu estado definido como o estado **preempção** . O lote tenta encontrar VMs de substituição e, se for bem-sucedido, os nós passarão pela **criação** e **inicialização** de Estados antes de ficarem disponíveis para a execução da tarefa, assim como novos nós.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Dimensionar um pool que contém VMs de baixa prioridade

Assim como acontece com os pools que consistem apenas em VMs dedicadas, é possível dimensionar um pool que contém VMs de baixa prioridade chamando o método redimensionar ou usando o dimensionamento automático.

A operação de redimensionamento do pool usa um segundo parâmetro opcional que atualiza o valor de **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A fórmula de autoescala do pool dá suporte a VMs de baixa prioridade da seguinte maneira:

-   Você pode obter ou definir o valor da variável definida pelo serviço **$TargetLowPriorityNodes**.

-   Você pode obter o valor da variável definida pelo serviço **$CurrentLowPriorityNodes**.

-   Você pode obter o valor da variável definida pelo serviço **$PreemptedNodeCount**. 
    Essa variável retorna o número de nós no estado preempção e permite que você Escale ou reduza verticalmente o número de nós dedicados, dependendo do número de nós admitidos que não estão disponíveis.

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas

Trabalhos e tarefas exigem pouca configuração adicional para nós de baixa prioridade; o único suporte é o seguinte:

-   A propriedade JobManagerTask de um trabalho tem uma nova propriedade, **AllowLowPriorityNode**. 
    Quando essa propriedade é verdadeira, a tarefa do Gerenciador de trabalho pode ser agendada em um nó dedicado ou de baixa prioridade. Se essa propriedade for false, a tarefa do Gerenciador de trabalho será agendada somente para um nó dedicado.

-   Uma [variável de ambiente](batch-compute-node-environment-variables.md) está disponível para um aplicativo de tarefa para que ele possa determinar se está sendo executado em um nó de baixa prioridade ou dedicado. A variável de ambiente é AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Manipulando preempção

Ocasionalmente, as VMs podem sofrer preempção; Quando a preempção acontece, o lote faz o seguinte:

-   As VMs admitidas têm seu estado atualizado para **preempção**.
-   Se as tarefas estivessem em execução nas VMs de nó admitidas anteriormente, essas tarefas serão recolocadas na fila e executadas novamente.
-   A VM é efetivamente excluída, levando à perda de quaisquer dados armazenados localmente na VM.
-   O pool tenta continuamente atingir o número de destino de nós de baixa prioridade disponíveis. Quando a capacidade de substituição é encontrada, os nós mantêm suas IDs, mas são reinicializados, passando pela **criação** e **inicialização** de Estados antes de estarem disponíveis para o agendamento de tarefas.
-   As contagens de preempção estão disponíveis como uma métrica no portal do Azure.

## <a name="metrics"></a>Métricas

Novas métricas estão disponíveis no [portal do Azure](https://portal.azure.com) para nós de baixa prioridade. Essas métricas são:

- Contagem de nós de baixa prioridade
- Contagem de núcleos de baixa prioridade 
- Contagem de nós preempção

Para exibir as métricas no portal do Azure:

1. Navegue até sua conta do lote no portal e exiba as configurações da sua conta do lote.
2. Selecione **métricas** na seção **monitoramento** .
3. Selecione as métricas que você deseja na lista de **métricas disponíveis** .

![Métricas para nós de baixa prioridade](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Passos seguintes

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Comece a planejar a mudança de VMs de baixa prioridade para identificar as VMs. Se você usar VMs de baixa prioridade com pools de **configuração de serviço de nuvem** , planeje a movimentação para os pools de configuração de **máquina virtual** .
