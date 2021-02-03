---
title: Executar cargas de trabalho em VMs de baixa prioridade com baixo nível de custos
description: Saiba como providenciar VMs de baixa prioridade para reduzir o custo das cargas de trabalho do Azure Batch.
author: mscurrell
ms.topic: how-to
ms.date: 02/02/2021
ms.custom: seodec18
ms.openlocfilehash: 9214ef83ec9b8bef4fb7bc7489aa0ab388f67c0d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507283"
---
# <a name="use-low-priority-vms-with-batch"></a>Utilizar VMs de baixa prioridade com o Batch

O Azure Batch oferece máquinas virtuais de baixa prioridade (VMs) para reduzir o custo das cargas de trabalho do Lote. Os VM de baixa prioridade tornam possíveis novos tipos de cargas de trabalho do Lote, permitindo que uma grande quantidade de energia de computação seja utilizada por um custo muito baixo.

Os VM de baixa prioridade aproveitam a capacidade excedentária em Azure. Quando especificar VMs de baixa prioridade nas suas piscinas, o Azure Batch pode utilizar este excedente, quando disponível.

A compensação pela utilização de VMs de baixa prioridade é que esses VMs podem nem sempre estar disponíveis para serem atribuídos, ou podem ser antecipados a qualquer momento, dependendo da capacidade disponível. Por esta razão, os VM de baixa prioridade são mais adequados para cargas de trabalho de processamento de lote e assíncronos, onde o tempo de conclusão do trabalho é flexível e o trabalho é distribuído por muitos VMs.

Os VM de baixa prioridade são oferecidos a um preço significativamente reduzido em comparação com os VM dedicados. Para obter detalhes sobre os preços, consulte [o Preço do Lote](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Os VM spot](https://azure.microsoft.com/pricing/spot/) estão agora disponíveis para conjuntos de escala [de VMs](../virtual-machines/spot-vms.md) e VM de [exemplo.](../virtual-machine-scale-sets/use-spot.md) Os VM spot são uma evolução de VMs de baixa prioridade, mas diferem nesse preço pode variar e um preço máximo opcional pode ser definido ao atribuir VMs spot.
>
> As piscinas Azure Batch começarão a suportar VMs spot dentro de alguns meses após a sua disponibilidade geral, com novas versões das [APIs do lote e ferramentas](./batch-apis-tools.md). Uma vez que o suporte spot VM esteja disponível, os VM de baixa prioridade serão depreciados - continuarão a ser suportados utilizando as atuais APIs e versões de ferramentas durante pelo menos 12 meses, para permitir tempo suficiente para a migração para os VMs spot.
>
> Os VMs spot não serão suportados para piscinas [de configuração de serviço](/rest/api/batchservice/pool/add#cloudserviceconfiguration) de nuvem. Para utilizar vMs spot, as piscinas de Cloud Service terão de ser migradas para piscinas [de configuração de máquinas virtuais.](/rest/api/batchservice/pool/add#virtualmachineconfiguration)

## <a name="batch-support-for-low-priority-vms"></a>Suporte de lote para VMs de baixa prioridade

O Azure Batch fornece várias capacidades que facilitam o consumo e beneficiam de VMs de baixa prioridade:

- As piscinas de lote podem conter VMs dedicados e VMs de baixa prioridade. O número de cada tipo de VM pode ser especificado quando uma piscina é criada, ou alterada a qualquer momento para uma piscina existente, usando a operação de redimensionamento explícito ou usando a escala automática. A submissão de trabalho e tarefa pode permanecer inalterada, independentemente dos tipos de VM na piscina. Você também pode configurar uma piscina para usar completamente VMs de baixa prioridade para executar empregos o mais barato possível, mas girar VMs dedicados se a capacidade cair abaixo de um limiar mínimo, para manter os empregos funcionando.
- Os pools de lote procuram automaticamente o número-alvo de VMs de baixa prioridade. Se os VMs estiverem preemptados ou indisponíveis, o Batch tenta substituir a capacidade perdida e regressar ao alvo.
- Quando as tarefas são interrompidas, o Batch deteta e recandidata automaticamente as tarefas para serem executadas novamente.
- Os VM de baixa prioridade têm uma quota vCPU separada que difere da dos VM dedicados. A quota para os VM de baixa prioridade é superior à quota para os VM dedicados, uma vez que os VM de baixa prioridade custam menos. Para obter mais informações, consulte [as quotas e limites de serviço do Batch.](batch-quota-limit.md#resource-quotas)

> [!NOTE]
> Os VM de baixa prioridade não são atualmente suportados para contas Batch criadas no [modo de subscrição do utilizador.](accounts.md)

## <a name="considerations-and-use-cases"></a>Considerações e processos de utilização

Muitas cargas de trabalho do Lote são um bom ajuste para VMs de baixa prioridade. Considere usá-los quando os empregos são divididos em muitas tarefas paralelas, ou quando você tem muitos empregos que são escalados e distribuídos por muitos VMs.

Alguns exemplos de casos de utilização de lotes adequados para utilizar VMs de baixa prioridade são:

- **Desenvolvimento e testes**: Em particular, se estiverem a ser desenvolvidas soluções em grande escala, podem ser realizadas poupanças significativas. Todos os tipos de testes podem beneficiar, mas os testes de carga em larga escala e os testes de regressão são excelentes utilizações.
- **Complementar a capacidade a pedido**: Os VM de baixa prioridade podem ser utilizados para complementar os VM dedicados regulares. Quando disponíveis, os postos de trabalho podem escalar e, portanto, completar mais rapidamente por custos mais baixos; quando não disponível, a linha de base de VMs dedicados permanece disponível.
- **Tempo flexível de execução do trabalho**: Se houver flexibilidade no tempo de trabalhos que têm de ser concluídos, então podem ser toleradas potenciais quebras de capacidade; no entanto, com a adição de empregos de baixa prioridade, os postos de trabalho são frequentemente executados mais rapidamente e por um custo mais baixo.

Os lotes podem ser configurados para utilizar VMs de baixa prioridade de algumas maneiras:

- Uma piscina só pode usar VMs de baixa prioridade. Neste caso, o Batch recupera qualquer capacidade pré-adequada quando disponível. Esta configuração é a forma mais barata de executar empregos.
- Os VM de baixa prioridade podem ser utilizados em conjunto com uma linha de base fixa de VMs dedicados. O número fixo de VMs dedicados garante que há sempre alguma capacidade para manter um emprego a progredir.
- Uma piscina pode usar uma mistura dinâmica de VMs dedicados e de baixa prioridade, de modo que os VMs de baixa prioridade mais baratos são usados apenas quando disponíveis, mas os VMs dedicados a preço inteiro são dimensionados quando necessário. Esta configuração mantém uma quantidade mínima de capacidade disponível para manter os postos de trabalho a progredir.

Tenha em mente o seguinte ao planear a sua utilização de VMs de baixa prioridade:

- Para maximizar a utilização da capacidade excedentária em Azure, os empregos adequados podem aumentar.
- Ocasionalmente, os VM podem não estar disponíveis ou ser preemptidos, o que resulta numa capacidade reduzida de empregos e pode levar à interrupção e repetições de tarefas.
- Tarefas com tempos de execução mais curtos tendem a funcionar melhor com VMs de baixa prioridade. Os postos de trabalho com tarefas mais longas podem ser mais afetados se forem interrompidos. Se as tarefas de longo prazo implementarem o checkpoint para salvar o progresso à medida que executam, este impacto pode ser reduzido. 
- Os trabalhos de MPI de longa duração que utilizam vários VMs não são adequados para usar VMs de baixa prioridade, porque um VM preempted pode levar a todo o trabalho ter que funcionar novamente.
- Os nós de baixa prioridade podem ser marcados como inutilizáveis se [as regras do grupo de segurança da rede (NSG)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) forem configuradas incorretamente.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Criar e gerir piscinas com VMs de baixa prioridade

Um pool de lote pode conter VMs dedicados e de baixa prioridade (também referidos como nós computacional). Pode definir o número-alvo de nós de computação para VMs dedicados e de baixa prioridade. O número de nós-alvo especifica o número de VMs que pretende ter na piscina.

Por exemplo, criar uma piscina utilizando máquinas virtuais Azure (neste caso, Os VMs Linux) com um alvo de 5 VMs dedicados e 20 VMs de baixa prioridade:

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

Pode obter o número atual de nós para VMs dedicados e de baixa prioridade:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Os nó de piscina têm uma propriedade para indicar se o nó é um VM dedicado ou de baixa prioridade:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Os VMs podem ocasionalmente ser antecipados. Quando isto acontece, as tarefas que estavam a ser executadas nos VMs de nó antecipado são requeadas e executadas novamente.

Para piscinas de configuração de máquinas virtuais, o Batch também faz o seguinte:

- Os VMs preempted têm o seu estado atualizado para **Preempted**. 
- O VM é efetivamente eliminado, levando à perda de quaisquer dados armazenados localmente no VM.
- Uma operação de nós de lista na piscina ainda devolverá os nós preemptados.
- A piscina tenta continuamente alcançar o número alvo de nós de baixa prioridade disponíveis. Quando a capacidade de substituição é encontrada, os nós mantêm os seus IDs, mas são reinicializados, **passando** por Estados **de Criação** e Início antes de estarem disponíveis para agendamento de tarefas.
- As contagens de preempção estão disponíveis como métrica no portal Azure.

## <a name="scale-pools-containing-low-priority-vms"></a>Piscinas de escala contendo VMs de baixa prioridade

Tal como acontece com piscinas compostas exclusivamente por VMs dedicados, é possível escalar uma piscina contendo VMs de baixa prioridade, chamando o método redimensionado ou utilizando a autoescala.

A operação de redimensionar a piscina requer um segundo parâmetro opcional que atualiza o valor dos **targetLowPriorityNodes:**

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A fórmula de escala automática da piscina suporta VMs de baixa prioridade da seguinte forma:

- Pode obter ou definir o valor da variável definida pelo serviço **$TargetLowPriorityNodes**.
- Pode obter o valor da variável definida pelo serviço **$CurrentLowPriorityNodes**.
- Pode obter o valor da variável definida pelo serviço **$PreemptedNodeCount**. Esta variável devolve o número de nós no estado preemptido e permite aumentar ou descer o número de nós dedicados, dependendo do número de nós pré-emitidos que não estão disponíveis.

## <a name="configure-jobs-and-tasks"></a>Configurar empregos e tarefas

Empregos e tarefas requerem pouca configuração adicional para nós de baixa prioridade. Tenha em atenção o seguinte:

- A propriedade jobManagerTask de um trabalho tem uma propriedade **AllowLowPriorityNode.** Quando esta propriedade é verdadeira, a tarefa de gerente de emprego pode ser agendada num nó dedicado ou de baixa prioridade. Se for falso, a tarefa de gerente de emprego está agendada para um nó dedicado apenas.
- A [variável ambiente](batch-compute-node-environment-variables.md) AZ_BATCH_NODE_IS_DEDICATED está disponível para uma aplicação de tarefa para que possa determinar se está a funcionar com baixa prioridade ou num nó dedicado.

## <a name="view-metrics-for-low-priority-vms"></a>Ver métricas para VMs de baixa prioridade

Novas métricas estão disponíveis no [portal Azure](https://portal.azure.com) para nós de baixa prioridade. Estas métricas são:

- Conde Low-Priority nó
- Contagem do Núcleo de Low-Priority
- Contagem de nódoaista preventiva

Para ver estas métricas no portal Azure

1. No portal do Azure, navegue para a sua conta do Batch.
2. Selecione **métricas** da secção **de monitorização.**
3. Selecione as métricas desejada na lista **métrica.**

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
- Comece a planear a mudança de VMs de baixa prioridade para Spot VMs. Se utilizar VMs de baixa prioridade com piscinas de **configuração do Cloud Service,** planeie migrar para [piscinas **de configuração de Máquina Virtual.**](nodes-and-pools.md#configurations)
