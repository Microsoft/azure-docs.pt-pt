---
title: Executar cargas de trabalho em VMs de baixa prioridade rentáveis
description: Aprenda a fornecer VMs de baixa prioridade para reduzir o custo das cargas de trabalho do Lote Azure.
author: mscurrell
ms.topic: how-to
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: 39c4674da2ddf63c5fab8b39e16a0cc0d9f299ac
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726592"
---
# <a name="use-low-priority-vms-with-batch"></a>Utilizar VMs de baixa prioridade com o Batch

O Azure Batch oferece máquinas virtuais de baixa prioridade (VMs) para reduzir o custo das cargas de trabalho do Lote. Os VMs de baixa prioridade tornam possível novos tipos de cargas de trabalho do Lote, permitindo que uma grande quantidade de energia computacional seja utilizada para um custo muito baixo.
 
Os VMs de baixa prioridade tiram partido da capacidade excedentária em Azure. Quando especifica VMs de baixa prioridade nas suas piscinas, o Lote Azure pode utilizar este excedente, quando disponível.
 
A compensação pela utilização de VMs de baixa prioridade é que esses VMs podem não estar disponíveis para serem atribuídos ou podem ser antecipados a qualquer momento, dependendo da capacidade disponível. Por esta razão, os VM de baixa prioridade são mais adequados para certos tipos de cargas de trabalho. Utilize VMs de baixa prioridade para cargas de trabalho de processamento de lote e assíncronos onde o tempo de conclusão do trabalho é flexível e o trabalho é distribuído por muitos VMs.
 
Os VM de baixa prioridade são oferecidos a um preço significativamente reduzido em comparação com os VMs dedicados. Para obter detalhes sobre preços, consulte [o Preço do Lote](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Os VMs spot](https://azure.microsoft.com/pricing/spot/) estão agora disponíveis para conjuntos de escala de [VMs](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)e VM de [uma só instância.](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) Os VMs spot são uma evolução de VMs de baixa prioridade, mas diferem na medida em que os preços podem variar e um preço máximo opcional pode ser definido ao alocar VMs spot.
>
> As piscinas do Lote Azure começarão a suportar Os VMs spot dentro de alguns meses após a sua disponibilização geral, com novas versões das [APIs e ferramentas](https://docs.microsoft.com/azure/batch/batch-apis-tools)do Lote. Uma vez disponível o suporte spot VM, os VMs de baixa prioridade serão depreciados - continuarão a ser suportados utilizando as atuais versões APIs e ferramentas durante pelo menos 12 meses, para permitir tempo suficiente para a migração para VMs spot. 
>
> Os VMs spot não serão suportados para piscinas de configuração de serviço em [nuvem.](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) Para utilizar Os VMs spot, as piscinas do Cloud Service terão de ser migradas para piscinas de configuração de [máquinas virtuais.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)


## <a name="use-cases-for-low-priority-vms"></a>Utilize casos para VMs de baixa prioridade

Dadas as características dos VMs de baixa prioridade, que cargas de trabalho podem ou não as utilizar? Em geral, as cargas de trabalho de processamento de lotes são um bom ajuste, uma vez que os postos de trabalho são divididos em muitas tarefas paralelas ou há muitos postos de trabalho que são dimensionados e distribuídos por muitas VMs.

-   Para maximizar a utilização da capacidade excedentária em Azure, os empregos adequados podem aumentar.

-   Ocasionalmente, as VMs podem não estar disponíveis ou preempted, o que resulta numa capacidade reduzida de empregos e pode levar à interrupção de tarefas e repetições. Por conseguinte, os postos de trabalho devem ser flexíveis no tempo que podem demorar a funcionar.

-   Os postos de trabalho com tarefas mais longas podem ser mais impactados se forem interrompidos. Se as tarefas de longa duração implementarem o controlo para poupar o progresso à medida que executam, o impacto da interrupção é reduzido. Tarefas com prazos de execução mais curtos tendem a funcionar melhor com VMs de baixa prioridade, porque o impacto da interrupção é muito menor.

-   Os trabalhos de MPI de longa duração que utilizam vários VMs não são adequados para utilizar VMs de baixa prioridade, porque um VM pré-escolhido pode levar a que todo o trabalho tenha de voltar a funcionar.

Alguns exemplos de processamento de lotes utilizam casos bem adequados para utilizar VMs de baixa prioridade:

-   **Desenvolvimento e testes**: Em particular, se estiverem a ser desenvolvidas soluções em larga escala, podem ser realizadas poupanças significativas. Todos os tipos de testes podem beneficiar, mas testes de carga em larga escala e testes de regressão são ótimos usos.

-   **Complementando a capacidade a pedido:** As VM de baixa prioridade podem ser utilizadas para complementar os VMs dedicados regulares - quando disponíveis, os postos de trabalho podem escalar e, portanto, completar mais rapidamente para custos mais baixos; quando não estão disponíveis, a linha de base de VMs dedicados permanece disponível.

-   Tempo flexível de **execução do emprego:** Se houver flexibilidade no tempo em que os postos de trabalho têm de ser concluídos, então podem ser toleradas potenciais quedas de capacidade; no entanto, com a adição de postos de trabalho de baixa prioridade, os postos de trabalho frequentemente funcionam mais rapidamente e por um custo mais baixo.

Os conjuntos de lotes podem ser configurados para utilizar VMs de baixa prioridade de algumas formas, dependendo da flexibilidade no tempo de execução do emprego:

-   VMs de baixa prioridade podem ser usados exclusivamente em uma piscina. Neste caso, o Batch recupera qualquer capacidade pré-empreitada quando disponível. Esta configuração é a forma mais barata de executar empregos, uma vez que apenas são utilizados VMs de baixa prioridade.

-   Os VMs de baixa prioridade podem ser utilizados em conjunto com uma linha de base fixa de VMs dedicados. O número fixo de VMs dedicados garante que há sempre alguma capacidade para manter um emprego em curso.

-   Pode haver uma mistura dinâmica de VMs dedicados e de baixa prioridade, de modo que os VMs de baixa prioridade mais baratos são usados apenas quando disponíveis, mas os VMs dedicados a preços completos são dimensionados quando necessário. Esta configuração mantém uma capacidade mínima disponível para manter os postos de trabalho em curso.

## <a name="batch-support-for-low-priority-vms"></a>Suporte de lote para VMs de baixa prioridade

O Azure Batch fornece várias capacidades que facilitam o consumo e beneficiam de VMs de baixa prioridade:

-   As piscinas de lotes podem conter vMs dedicados e VMs de baixa prioridade. O número de cada tipo de VM pode ser especificado quando uma piscina é criada, ou alterada a qualquer momento para uma piscina existente, utilizando a operação de redimensionamento explícito ou utilizando a escala automática. A submissão de trabalho e tarefa pode permanecer inalterada, independentemente dos tipos de VM na piscina. Você também pode configurar uma piscina para usar completamente VMs de baixa prioridade para executar empregos o mais barato possível, mas girar para cima VMs dedicados se a capacidade cair abaixo de um limiar mínimo, para manter os empregos em funcionamento.

-   Os pools de lotes procuram automaticamente o número-alvo de VMs de baixa prioridade. Se os VMs forem preempted, então o Batch tenta substituir a capacidade perdida e voltar ao alvo.

-   Quando as tarefas são interrompidas, o Batch deteta e refaz automaticamente as tarefas para executar novamente.

-   Os VMs de baixa prioridade têm uma quota vCPU separada que difere da dos VMs dedicados. 
    A quota para VMs de baixa prioridade é superior à quota para VMs dedicados, porque os VM sde de baixa prioridade custam menos. Para mais informações, consulte quotas e limites de [serviço do Lote.](batch-quota-limit.md#resource-quotas)    

> [!NOTE]
> Os VMs de baixa prioridade não são atualmente suportados para as contas do Lote criadas no modo de [subscrição](batch-api-basics.md#account)do utilizador .
>

## <a name="create-and-update-pools"></a>Criar e atualizar piscinas

Um pool de lote pode conter VMs dedicados e de baixa prioridade (também referidos como nós de computação). Pode definir o número de nós de cálculo para VMs dedicados e de baixa prioridade. O número alvo de nós especifica o número de VMs que deseja ter na piscina.

Por exemplo, criar uma piscina utilizando VMs de serviço de nuvem Azure com um alvo de 5 VMs dedicados e 20 VMs de baixa prioridade:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Para criar uma piscina utilizando máquinas virtuais Azure (neste caso, VMs Linux) com um alvo de 5 VMs dedicados e 20 VMs de baixa prioridade:

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

Você pode obter o número atual de nós para VMs dedicados e de baixa prioridade:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Os nódosos da piscina têm uma propriedade para indicar se o nó é um VM dedicado ou de baixa prioridade:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Quando um ou mais nós numa piscina são preempted, uma operação de nós de lista na piscina ainda devolve esses nós. O número atual de nós de baixa prioridade permanece inalterado, mas esses nós têm o seu estado definido para o Estado **Preempted.** O lote tenta encontrar VMs de substituição e, se for bem sucedido, os nós passam por **Criar** e, em seguida, **começar** estados antes de se tornarem disponíveis para execução de tarefas, assim como novos nós.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Escala rema uma piscina contendo VMs de baixa prioridade

Tal como acontece com piscinas exclusivamente constituídas por VMs dedicados, é possível escalar uma piscina contendo VMs de baixa prioridade, chamando o método Resize ou utilizando a escala automática.

A operação de redimensionação da piscina requer um segundo parâmetro opcional que atualiza o valor dos **targetLowPriorityNodes:**

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A fórmula de escala automática da piscina suporta VMs de baixa prioridade da seguinte forma:

-   Pode obter ou definir o valor da variável definida pelo serviço **$TargetLowPriorityNodes**.

-   Pode obter o valor da variável definida pelo serviço **$CurrentLowPriorityNodes**.

-   Pode obter o valor da variável definida pelo serviço **$PreemptedNodeCount**. 
    Esta variável devolve o número de nós no estado pré-optado e permite-lhe escalar para cima ou para baixo o número de nós dedicados, dependendo do número de nós pré-optados que não estão disponíveis.

## <a name="jobs-and-tasks"></a>Empregos e tarefas

Empregos e tarefas requerem pouca configuração adicional para nós de baixa prioridade; o único suporte é o seguinte:

-   A propriedade JobManagerTask de um trabalho tem uma nova propriedade, **AllowLowPriorityNode**. 
    Quando esta propriedade é verdadeira, a tarefa do gestor de emprego pode ser agendada num nó dedicado ou de baixa prioridade. Se esta propriedade for falsa, a tarefa do gestor de emprego está agendada apenas para um nó dedicado.

-   Uma [variável ambiental](batch-compute-node-environment-variables.md) está disponível para uma aplicação de tarefa para que possa determinar se está a funcionar com um nó de baixa prioridade ou dedicado. A variável ambiental é AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Manuseamento de preempção

As VMs podem ocasionalmente ser preempted; quando a preempção acontece, o Batch faz o seguinte:

-   Os VMs pré-empreitados têm o seu estado atualizado para **Preempted**.
-   Se as tarefas estavam a decorrer nos VMs de nós pré-optados, então essas tarefas são ressonadas e executadas novamente.
-   O VM é efetivamente eliminado, levando à perda de quaisquer dados armazenados localmente no VM.
-   A piscina continuamente tenta atingir o número alvo de nós de baixa prioridade disponíveis. Quando a capacidade de substituição é encontrada, os nós mantêm as suas identificações, mas são reinicializados, passando pelos estados **de Criação** e **Início** antes de estarem disponíveis para agendamento de tarefas.
-   As contagens de preempção estão disponíveis como métrica no portal Azure.

## <a name="metrics"></a>Métricas

Estão disponíveis novas métricas no [portal Azure](https://portal.azure.com) para nódeos de baixa prioridade. Estas métricas são:

- Contagem de nódeadebaixas
- Contagem do núcleo de baixa prioridade 
- Contagem de nósinos preempted

Para ver as métricas no portal Azure:

1. Navegue para a sua conta De lote no portal e veja as definições para a sua conta Batch.
2. Selecione **Métricas** da secção **de Monitorização.**
3. Selecione as métricas que deseja na lista de **Métricas Disponíveis.**

![Métricas para nódeos de baixa prioridade](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Próximos passos

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Comece a planear a mudança de VMs de baixa prioridade para VMs spot. Se utilizar VMs de baixa prioridade com piscinas de configuração do **Cloud Service,** planeie mudar-se para piscinas de **configuração de Máquina Virtual.**
