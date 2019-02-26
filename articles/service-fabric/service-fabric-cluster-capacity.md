---
title: Planear a capacidade de cluster do Service Fabric | Documentos da Microsoft
description: Capacidade de cluster do Service Fabric considerações de planeamento. Escalões Nodetypes, operações, durabilidade e fiabilidade
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: 5fb8f54f50d821e53ec260c67ad5cf56c7f5671b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816543"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planeamento de capacidade do cluster de Service Fabric
Para qualquer implementação de produção, planeamento de capacidade é um passo importante. Aqui estão alguns dos itens que deve considerar como parte desse processo.

* O número de tipos de nó que do cluster tem de começar com
* As propriedades de cada tipo de nó (tamanho, primário, de acesso à internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

> [!NOTE]
> No mínimo deve rever todos os **não permitida** atualizar valores de política durante o planejamento. Isso é para garantir que defina os valores adequadamente e para atenuar a gravação para baixo do seu cluster mais tarde devido a definições de configuração do sistema inalterável. 
> 

Diga-numa rápida análise cada um desses itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nó que do cluster tem de começar com
Em primeiro lugar, terá de descobrir o que o cluster que estiver a criar irá ser utilizado para.  Que tipos de aplicativos estiver a planear implementar para este cluster? Se não for clara sobre a finalidade do cluster, é mais provável que não ainda está preparado para introduzir a processo de planeamento da capacidade.

Estabelece o número de tipos de nó que tem de começar com o cluster.  Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquina virtual. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Portanto, a decisão do número de tipos de nó essencialmente se resume às seguintes considerações:

* A aplicação tem vários serviços, e qualquer um deles precisa de ser público ou com acesso à internet? Os aplicativos típicos de conter um serviço de gateway de front-end que recebe a entrada de um cliente e um ou mais serviços back-end que se comunicam com os serviços front-end. Então, neste caso, acaba tendo, pelo menos, dois tipos de nó.
* Seus serviços (que tornam a sua aplicação) tem necessidades de infraestrutura diferentes, como o maior de RAM ou superior ciclos de CPU? Por exemplo, vamos assuma que contém a aplicação que pretende implementar um serviço de front-end e um serviço de back-end. O serviço de front-end pode executar em VMs menores (tamanhos de VM, como D2) que têm portas abertas para a internet.  O serviço de back-end, no entanto, está intensivas de computação e tem de ser executadas nas VMs maiores (com tamanhos de VM, como D4, D6, D15) que não são internet a ter.
  
  Neste exemplo, embora pode decidir colocar todos os serviços num tipo de nó, recomendamos que coloque-os num cluster com dois tipos de nó.  Isso permite que cada tipo de nó ter propriedades distintas, como conectividade à internet ou de tamanho da VM. O número de VMs pode ser dimensionado de forma independente, bem.  
* Porque não é possível prever o futuro, vá com fatos, que sabe e escolha o número de tipos de nó que seus aplicativos precisam para começar. Pode sempre adicionar ou remover tipos de nó mais tarde. Um cluster do Service Fabric tem de ter, pelo menos, um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** podem ser vistos como equivalentes às funções nos serviços Cloud. Tipos de nó definem os tamanhos VM, o número de VMs e as respetivas propriedades. Cada tipo de nó que está definido no cluster do Service Fabric é mapeado para um [conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Cada tipo de nó é um dimensionamento distinto definida e pode ser aumentado ou para baixo de forma independente, têm conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Para obter mais informações sobre as relações entre os tipos de nós e conjuntos de dimensionamento de máquinas virtuais, como fazer o RDP para uma das instâncias, como abrir novas portas e assim por diante, consulte [tipos de nós de cluster do Service Fabric](service-fabric-cluster-nodetypes.md).

Um cluster do Service Fabric pode consistir em mais de um tipo de nó. De eventos, o cluster consiste num tipo de nó primário e um ou mais tipos de nó não principal.

Tipo de nó único não é possível dimensione em segurança para além de 100 nós por conjunto para aplicativos de SF; de dimensionamento de máquina virtual alcançar maior do que 100 nós fiável, exigirá que adicione conjuntos de dimensionamento de máquina virtual adicionais.

### <a name="primary-node-type"></a>Tipo de nó principal

Os serviços de sistema do Service Fabric (por exemplo, o serviço de Gestor de clusters ou um serviço de imagem Store) são colocados no tipo de nó primário. 

![Captura de ecrã de um cluster que tem dois tipos de nó][SystemServices]

* O **tamanho mínimo de VMs** para o nó principal tipo é determinado pela **escalão de durabilidade** que escolher. O escalão de durabilidade de padrão é Bronze. Ver [as características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) para obter mais detalhes.  
* O **número mínimo de VMs** para o nó principal tipo é determinado pela **escalão de fiabilidade** que escolher. O escalão de fiabilidade de padrão é Silver. Ver [as características de fiabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) para obter mais detalhes.  

Ao modelo Azure Resource Manager, o tipo de nó principal está configurado com o `isPrimary` atributo sob o [definição de tipo de nó](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Tipo de nó não-primário

Num cluster com vários tipos de nó, existe um tipo de nó primário e o restante é não principal.

* O **tamanho mínimo de VMs** para o nó não primário tipos é determinado pela **escalão de durabilidade** que escolher. O escalão de durabilidade de padrão é Bronze. Para obter mais informações, consulte [as características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* O **número mínimo de VMs** para tipos de nós não primário é um. No entanto, deve escolher esse número com base no número de réplicas de aplicações/serviços que pretende executar neste tipo de nó. O número de VMs num tipo de nó pode ser aumentado depois de ter implementado o cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster
O escalão de durabilidade é utilizado para indicar ao sistema os privilégios que tem das suas VMs com a infraestrutura do Azure subjacente. O tipo de nó primário, esse privilégio permite que o Service Fabric colocar em pausa qualquer solicitação de infraestrutura de nível de VM (por exemplo, um reinício da VM, uma recriação de imagem de VM ou uma migração de VM) que têm impacto sobre os requisitos de quórum para os serviços do sistema e seus serviços com estado. Os tipos de nó não primário, esse privilégio permite que o Service Fabric colocar em pausa quaisquer solicitações de infraestrutura de nível de VM (por exemplo, o reinício da VM, a recriação de imagem de VM e a migração de VM) que têm impacto sobre os requisitos de quórum para os seus serviços com estado.

| Escalão de durabilidade  | Número mínimo necessário de VMs | SKUs de VM suportadas                                                                  | Atualizações feitas ao conjunto de dimensionamento de máquina virtual                               | Atualizações e manutenção iniciada pelo Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Dourado             | 5                              | SKUs de full-nó dedicados de um único cliente (por exemplo, L32s, GS5, G5, DS15_v2, D15_v2) | Pode ser atrasada até aprovadas pelo cluster do Service Fabric | Pode ser colocada em pausa durante 2 horas por UD para dar tempo adicional para réplicas de recuperar de falhas anteriores |
| Prateado           | 5                              | VMs de núcleo único ou superior                                                        | Pode ser atrasada até aprovadas pelo cluster do Service Fabric | Não pode ser atrasada durante qualquer período significativo de tempo                                                    |
| Bronze           | 1                              | Todos                                                                                | Cluster do Service Fabric não será adiado           | Não pode ser atrasada durante qualquer período significativo de tempo                                                    |

> [!WARNING]
> Obter tipos de nós em execução com durabilidade de Bronze _sem privilégios_. Isso significa que as tarefas de infraestrutura que afetam as cargas de trabalho sem monitorização de estado serão não ser paradas ou atrasadas, que podem afetar as suas cargas de trabalho. Utilize apenas Bronze para tipos de nós que executam apenas as cargas de trabalho sem monitorização de estado. Para cargas de trabalho de produção, em execução Silver ou superior é recomendado. 

> Independentemente de qualquer nível de durabilidade [Desalocação](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) operação no conjunto de dimensionamento de VM irá destruir o cluster

**Vantagens da utilização de níveis de durabilidade de Gold ou Silver**
 
- Reduz o número de passos necessários numa operação de redução horizontal (ou seja, a desativação de nó e Remove-ServiceFabricNodeState é chamado automaticamente).
- Reduz o risco de perda de dados devido a operações de infraestrutura do Azure ou de operação de alteração um SKU de VM de in-loco iniciadas pelo cliente.

**Desvantagens da utilização de níveis de durabilidade de Gold ou Silver**
 
- As implementações para o dimensionamento de máquinas virtuais, defina e outro relacionados com recursos do Azure podem ser atrasados, podem esgotar o tempo limite ou podem ser bloqueados totalmente por problemas no seu cluster ou ao nível da infraestrutura. 
- Aumenta o número de [eventos de ciclo de vida de réplica](service-fabric-reliable-services-lifecycle.md) (por exemplo, trocas primárias) devido a automatizada desativações nó durante as operações de infraestrutura do Azure.
- Se nós fora de serviço por períodos de tempo enquanto as atualizações de software de plataforma do Azure ou a manutenção de hardware atividades estão a ocorrer. Poderá ver nós com o estado de desabilitar/desativado durante essas atividades. Isso reduz a capacidade do seu cluster temporariamente, mas não deve afetar a disponibilidade das suas aplicações ou de cluster.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Recomendações para quando utilizar níveis de durabilidade de Gold ou Silver

Utilizar Gold ou Silver durabilidade para todos os tipos de nós que alojam serviços com estado pretende reduzir (reduzir o número de instâncias VM) com frequência, e desejar que as operações de implementação ser atrasada e a capacidade para ser reduzida em favor de simplificar essas dimensionamento-in operações. Os cenários de escalamento horizontal (aumento as instâncias de VMs) não se encaixam na sua escolha de escalão de durabilidade, dimensionamento-in só faz.

### <a name="changing-durability-levels"></a>Alterar os níveis de durabilidade
- Tipos de nós com níveis de durabilidade de Gold ou Silver não podem ser desatualizados para Bronze.
- A atualização de Bronze para Gold ou Silver pode levar algumas horas.
- Quando muda o nível de durabilidade, certifique-se de que atualizá-lo na configuração dos recursos de infraestrutura do serviço de extensão no seu recurso de conjunto de dimensionamento de máquina virtual e, na definição do tipo de nó no seu recurso de cluster do Service Fabric. Estes valores têm de corresponder.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendações operacionais para o nó escreva que definiu para durabilidade silver ou gold nível.

- Manter o cluster e as aplicações em bom estado durante todo o tempo e certifique-se de que os aplicativos respondem a todos [eventos de ciclo de vida de réplica do serviço](service-fabric-reliable-services-lifecycle.md) (como a réplica de compilação está bloqueada) no momento oportuno.
- Adote o mais seguras maneiras de tornar um SKU de VM alterar (horizontal/vertical): Alterar o SKU de VM de um conjunto de dimensionamento de máquina virtual é, inerentemente, uma operação não segura e por isso, deve ser evitado se possível. Eis o processo que pode seguir para evitar problemas comuns.
    - **Para tipos de nó não primário:** Recomenda-se que crie o novo conjunto de dimensionamento de máquina virtual, modificar a restrição de posicionamento de serviço para incluir o novo tipo de conjunto/nó de dimensionamento de máquina virtual e, em seguida, reduzir a contagem de instâncias de conjunto de dimensionamento a antiga máquina virtual como zero, um nó por vez (isto é tornar -se de que a remoção de nós não afetam a fiabilidade do cluster).
    - **Para o tipo de nó primário:** Nossa recomendação é que não altere os SKU de VM do tipo de nó primário. A alteração do tipo de nó primário que SKU não é suportado. Se o motivo para o novo SKU é a capacidade, recomendamos que adicione mais instâncias. Se isso não é possível, crie um novo cluster e [restaurar estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo. Não é necessário restaurar o estado do serviço qualquer sistema, eles são recriados quando implanta aplicativos para o novo cluster. Se estiver a executar aplicativos sem monitoração de estado no seu cluster, implante seus aplicativos para o novo cluster.  Não têm nada a restaurar. Se optar por seguir o caminho não suportado e quiser alterar o SKU de VM, em seguida, fazer modificações para o dimensionamento de máquinas virtuais definição de modelo para refletir o novo SKU do conjunto. Se o cluster tem apenas um tipo de nó, em seguida, certifique-se de que todas as suas aplicações com monitorização de estado a responder a todos [eventos de ciclo de vida de réplica do serviço](service-fabric-reliable-services-lifecycle.md) (como a réplica de compilação está bloqueada) na forma oportuna e que a réplica de serviço reconstruir duração é menos de cinco minutos (para o nível de durabilidade Silver). 
    
- Manter uma contagem mínima de cinco nós para qualquer conjunto de dimensionamento de máquina virtual que tem o nível de durabilidade de Gold ou Silver ativada.
- Cada conjunto de dimensionamento com o nível de durabilidade Gold ou Silver tem de mapear para o seu próprio tipo de nó no cluster do Service Fabric. O mapeamento de vários conjuntos de dimensionamento de máquina virtual para um tipo de nó único, impedirá que coordenação entre o cluster do Service Fabric e a infraestrutura do Azure a funcionar corretamente.
- Não eliminar instâncias de VM aleatórias, utilize sempre o dimensionamento de conjunto de dimensionamento de máquinas virtuais para baixo de funcionalidade. A eliminação aleatórias de instâncias de VM tem um potencial de criação de desequilíbrios na instância de VM-se distribuídas por UD e FD. Esse desequilíbrio pode afetar negativamente a capacidade de sistemas corretamente o balanceamento de carga entre as réplicas do serviço/instâncias de serviço.
- Se utilizar o dimensionamento automático, defina as regras, de modo a que o dimensionamento em (remover de instâncias de VM) são feitas apenas um nó por vez. Não é seguro reduzir verticalmente mais de uma instância de cada vez.
- Se eliminar ou a desalocar VMs no tipo de nó primário, nunca deve reduzir a contagem de VMs alocadas abaixo o que requer o escalão de fiabilidade. Estas operações serão bloqueadas indefinidamente num conjunto de dimensionamento com um nível de durabilidade de Gold ou Silver.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de fiabilidade do cluster
O escalão de fiabilidade é usado para definir o número de réplicas dos serviços do sistema que deseja executar neste cluster, o tipo de nó primário. Mais o número de réplicas, mais confiáveis são os serviços do sistema no seu cluster.  

O escalão de fiabilidade pode realizar os seguintes valores:

* Platinum - executar os serviços do sistema com um destino de contagem de sete do conjunto de réplicas
* Ouro - executar os serviços do sistema com um destino de contagem de sete do conjunto de réplicas
* Silver - executar os serviços do sistema com um destino de contagem de cinco do conjunto de réplicas 
* Bronze - executar os serviços do sistema com um destino de contagem de três do conjunto de réplicas

> [!NOTE]
> O escalão de fiabilidade que escolhe determina o número mínimo de nós que tem de ter o tipo de nó principal. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Recomendações para o escalão de fiabilidade

Quando aumenta ou diminuir o tamanho do cluster (a soma das instâncias VM em todos os tipos de nó), tem de atualizar a fiabilidade do seu cluster de um escalão para outro. Fazer isto aciona as atualizações de cluster necessário para alterar a réplica de serviços do sistema, conjunto de contagem. Aguarde que a atualização em curso para concluir antes de efetuar outras alterações ao cluster, como adicionar nós.  Pode monitorizar o progresso da atualização no Service Fabric Explorer ou da execução [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Eis a recomendação sobre como escolher o escalão de fiabilidade.  O número de nós de semente também é definido como o número mínimo de nós para um escalão de fiabilidade.  Por exemplo, para um cluster com fiabilidade de ouro há 7 nós de semente.

| **Número de nós de cluster** | **Escalão de fiabilidade** |
| --- | --- |
| 1 |Não especifique o parâmetro de escalão de fiabilidade, o sistema calcula-lo |
| 3 |Bronze |
| 5 ou 6|Prateado |
| 7 ou 8 |Dourado |
| 9 e até |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó primário - diretrizes de capacidade

Eis as diretrizes para planear a capacidade de tipo de nó primário:

- **Número de instâncias VM para executar qualquer carga de trabalho de produção no Azure:** Tem de especificar um tamanho de tipo de nó primário mínimo de 5 e um escalão de fiabilidade de prata.  
- **Número de instâncias VM para executar cargas de trabalho de teste no Azure** pode especificar um tamanho de tipo de nó primário mínimo de 1 ou 3. O cluster de um nó, é executado com uma configuração especial e isso, horizontal fora desse cluster não é suportado. O cluster de um nó, tem não confiabilidade e por isso, no modelo do Resource Manager, tem de remover/não especificar que a configuração (não definir o valor de configuração não é suficiente). Se configurar o cluster de um nó configurar através do portal, em seguida, a configuração é feita automaticamente. Um e três clusters de nós não são suportadas para executar cargas de trabalho de produção. 
- **SKU DE VM:** Tipo de nó principal é onde executam os serviços do sistema, para que o SKU de VM que escolher para o mesmo, deve fazer em conta o pico geral carregar pretende colocar em cluster. Aqui está uma analogia para ilustrar o que quero dizer aqui – considerar o tipo de nó primário como seu "Lungs", é o que fornece oxygen para seu cérebro e, portanto, se o cérebro não é suficiente oxygen, o seu corpo sofre. 

Uma vez que as necessidades de capacidade de um cluster é determinado pela carga de trabalho que pretende executar no cluster, não é possível fornecemos com qualitativos orientações para a sua carga de trabalho específica, no entanto aqui é a ampla orientação para ajudá-lo a começar

Para cargas de trabalho de produção: 

- É recomendado para dedicar os seus clusters NodeType primário para serviços do sistema e restrições de posicionamento de utilização para implementar a aplicação NodeTypes secundário.
- O SKU de VM recomendados é D3 padrão ou D3_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD.
- O uso de suporte mínimo SKU de VM é Standard D1 ou D1_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD. 
- 14 GB SSD local é um requisito mínimo. Nossa recomendação é um mínimo de 50 GB. Para cargas de trabalho, especialmente ao executar contentores do Windows, discos maiores são necessários. 
- Principais parcial SKUs de VM, como Standard A0 não são suportadas para cargas de trabalho de produção.
- O SKU Standard A1 não é suportado para cargas de trabalho de produção por motivos de desempenho.
- VMs de baixa prioridade não são suportadas.

> [!WARNING]
> Alterar o tamanho de SKU de VM num cluster em execução, de nó principal é uma operação de dimensionamento e documentados em [conjunto de dimensionamento de Máquina Virtual de aumento horizontal](virtual-machine-scale-set-scale-node-type-scale-out.md) documentação.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo de nó de não-primary - diretrizes de capacidade para cargas de trabalho com monitorização de estado

Esta orientação destina cargas de trabalho com monitorização de estado com o Service fabric [coleções fiáveis ou dos reliable Actors](service-fabric-choose-framework.md) que está a executar o tipo de nó não principal.

**Número de instâncias VM:** Para produção as cargas de trabalho com monitorização de estado, recomenda-se que executá-los com uma contagem de réplica e o destino da mínimo de 5. Isso significa que no estado estável pode ficar com uma réplica (a partir de um conjunto de réplicas) em cada domínio de falha e o domínio de atualização. O conceito de escalão de fiabilidade inteiro para o tipo de nó primário é uma forma de especificar esta definição para serviços do sistema. Portanto, a mesma consideração aplica-se aos seus serviços com estado também.

Portanto, para cargas de trabalho de produção, o tamanho de tipo mínimo recomendado não - nó primário é 5, se estiver a executar cargas de trabalho com monitorização de estado no mesmo.

**SKU DE VM:** Isso é o tipo de nó em que estiver a executar os serviços de aplicativo, então o SKU de VM que escolher para ele, deve levar em conta o pico de carga que pretende colocar em cada nó. As necessidades de capacidade do tipo de nó, é determinado pela carga de trabalho que pretende executar no cluster, pelo que não é possível fornecemos com qualitativos orientações para a sua carga de trabalho específica, no entanto aqui é a ampla orientação para ajudá-lo a começar

Para cargas de trabalho de produção 

- O SKU de VM recomendados é D3 padrão ou D3_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD.
- O uso de suporte mínimo SKU de VM é Standard D1 ou D1_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD. 
- Principais parcial SKUs de VM, como Standard A0 não são suportadas para cargas de trabalho de produção.
- O SKU Standard A1 não é suportado para cargas de trabalho de produção por motivos de desempenho.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo de nó de não-primary - diretrizes de capacidade para cargas de trabalho sem monitorização de estado

Esta orientação de cargas de trabalho sem monitorização de estado que está a executar o tipo de nó não principal.

**Número de instâncias VM:** Para produção as cargas de trabalho sem monitorização de estado, o tamanho de tipo de mínimo suportado não - nó primário é 2. Isso permite que executar duas instâncias sem monitoração de estado da sua aplicação e permitir que o seu serviço para sobreviver à perda de uma instância de VM. 

**SKU DE VM:** Isso é o tipo de nó em que estiver a executar os serviços de aplicativo, então o SKU de VM que escolher para ele, deve levar em conta o pico de carga que pretende colocar em cada nó. As necessidades de capacidade do tipo de nó é determinado pela carga de trabalho que pretende executar no cluster. Não é possível disponibilizamos qualitativos orientações para a sua carga de trabalho específica.  No entanto, aqui está a ampla orientação para ajudar a começar.

Para cargas de trabalho de produção 

- O SKU de VM recomendados é D3 padrão ou D3_V2 padrão ou equivalente. 
- O uso de suporte mínimo SKU de VM é Standard D1 ou D1_V2 padrão ou equivalente. 
- Principais parcial SKUs de VM, como Standard A0 não são suportadas para cargas de trabalho de produção.
- O SKU Standard A1 não é suportado para cargas de trabalho de produção por motivos de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passos Seguintes
Depois de concluir o planeamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança do cluster do Service Fabric](service-fabric-cluster-security.md)
* [Dimensionamento de clusters do Service Fabric](service-fabric-cluster-scaling.md)
* [Planejamento de recuperação após desastre](service-fabric-disaster-recovery.md)
* [Definir a relação de Nodetypes com dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
