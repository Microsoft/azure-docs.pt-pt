---
title: Planejando a capacidade de Cluster Service Fabric | Microsoft Docs
description: Service Fabric considerações de planejamento da capacidade do cluster. Tipos de NodeTypes, operações, durabilidade e confiabilidade
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2019
ms.author: chackdan
ms.openlocfilehash: 2d13364093776028f96b75c5bfef252e2fdfc790
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679405"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planejamento de capacidade de Cluster Service Fabric
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns dos itens que você precisa considerar como parte desse processo.

* O número de tipos de nó que seu cluster precisa iniciar com
* As propriedades de cada tipo de nó (tamanho, primário, voltado para a Internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

> [!NOTE]
> Você deve revisar minimamente todos os valores de política de atualização **não permitidos** durante o planejamento. Isso é para garantir que você defina os valores adequadamente e para atenuar a gravação de seu cluster mais tarde, devido a configurações de configuração do sistema inalteráveis. 
> 

Vamos examinar brevemente cada um desses itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nó que seu cluster precisa iniciar com
Primeiro, você precisa descobrir o que o cluster que está criando será usado para o.  Que tipos de aplicativos você pretende implantar nesse cluster? Se você não estiver claro quanto à finalidade do cluster, provavelmente ainda não estará pronto para inserir o processo de planejamento de capacidade.

Estabeleça o número de tipos de nós com os quais o cluster precisa começar.  Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquinas virtuais. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Portanto, a decisão do número de tipos de nó essencialmente se resume às seguintes considerações:

* Seu aplicativo tem vários serviços e qualquer um deles precisa ser público ou voltado para a Internet? Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que se comunicam com os serviços de front-end. Nesse caso, você acaba tendo pelo menos dois tipos de nó.
* Seus serviços (que compõem seu aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Por exemplo, vamos supor que o aplicativo que você deseja implantar contenha um serviço de front-end e um serviço de back-end. O serviço de front-end pode ser executado em VMs menores (tamanhos de VM, como D2) que têm portas abertas para a Internet.  O serviço de back-end, no entanto, é de computação intensiva e precisa ser executado em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltados para a Internet.
  
  Neste exemplo, embora você possa decidir colocar todos os serviços em um tipo de nó, recomendamos colocá-los em um cluster com dois tipos de nó.  Isso permite que cada tipo de nó tenha propriedades distintas, como conectividade da Internet ou tamanho da VM. O número de VMs também pode ser dimensionado de forma independente.  
* Como não é possível prever o futuro, vá com os fatos que você conhece e escolha o número de tipos de nós com os quais seus aplicativos precisam começar. Você sempre pode adicionar ou remover tipos de nó mais tarde. Um Cluster Service Fabric deve ter pelo menos um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** pode ser visto como equivalente às funções nos serviços de nuvem. Os tipos de nó definem os tamanhos de VM, o número de VMs e suas propriedades. Cada tipo de nó definido em um cluster de Service Fabric é mapeado para um conjunto de dimensionamento de [máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Cada tipo de nó é um conjunto de dimensionamento distinto e pode ser aumentado ou reduzido de forma independente, ter diferentes conjuntos de portas abertas e ter métricas de capacidade diferentes. Para obter mais informações sobre as relações entre os tipos de nó e os conjuntos de dimensionamento de máquinas virtuais, como RDP em uma das instâncias, como abrir novas portas e assim por diante, consulte [Service Fabric tipos de nó de cluster](service-fabric-cluster-nodetypes.md).

Um Cluster Service Fabric pode consistir em mais de um tipo de nó. Nesse evento, o cluster consiste em um tipo de nó primário e um ou mais tipos de nós não primários.

Um tipo de nó único não pode ser dimensionado de maneira confiável além de 100 nós por conjunto de dimensionamento de máquinas virtuais para aplicativos da it; atingir mais de 100 nós de forma confiável exigirá que você adicione conjuntos de dimensionamento de máquinas virtuais adicionais.

### <a name="primary-node-type"></a>Tipo de nó primário

Os serviços do sistema Service Fabric (por exemplo, o serviço do Gerenciador de cluster ou serviço Repositório de Imagens) são colocados no tipo de nó primário. 

![Captura de tela de um cluster que tem dois tipos de nó][SystemServices]

* O **tamanho mínimo de VMs** para o tipo de nó primário é determinado pela **camada** de durabilidade que você escolher. A camada de durabilidade padrão é bronze. Consulte [as características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) para obter mais detalhes.  
* O **número mínimo de VMs** para o tipo de nó primário é determinado pela **camada de confiabilidade** que você escolher. A camada de confiabilidade padrão é prata. Consulte [as características de confiabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) para obter mais detalhes.  

No modelo de Azure Resource Manager, o tipo de nó primário é configurado com `isPrimary` o atributo sob a [definição de tipo de nó](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Tipo de nó não primário

Em um cluster com vários tipos de nó, há um tipo de nó primário e o restante são não primários.

* O **tamanho mínimo de VMs** para tipos de nó não primário é determinado pela **camada** de durabilidade que você escolher. A camada de durabilidade padrão é bronze. Para obter mais informações, consulte [as características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* O **número mínimo de VMs** para tipos de nó não primário é um. No entanto, você deve escolher esse número com base no número de réplicas do aplicativo/serviços que você deseja executar nesse tipo de nó. O número de VMs em um tipo de nó pode ser aumentado depois que você implantou o cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que suas VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que Service Fabric Pause qualquer solicitação de infraestrutura de nível de VM (como uma reinicialização de VM, reimagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e seus serviços com estado. Nos tipos de nó não primários, esse privilégio permite que Service Fabric Pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, reimagem de VM e migração de VM) que afetem os requisitos de quorum para seus serviços com estado.

| Camada de durabilidade  | Número mínimo necessário de VMs | SKUs de VM com suporte                                                                  | Atualizações feitas no conjunto de dimensionamento de máquinas virtuais                               | Atualizações e manutenção iniciadas pelo Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Dourado             | 5                              | SKUs de nó completo dedicados a um único cliente (por exemplo, L32s, GS5, G5, DS15_v2, D15_v2) | Pode ser atrasada até ser aprovado pelo Cluster Service Fabric | Pode ser pausado por 2 horas por UD para permitir tempo adicional para que as réplicas se recuperem de falhas anteriores |
| Prateado           | 5                              | VMs de núcleo único ou superior com pelo menos 50 GB de SSD local                      | Pode ser atrasada até ser aprovado pelo Cluster Service Fabric | Não pode ser atrasada por nenhum período de tempo significativo                                                    |
| Bronze           | 1                              | VMs com pelo menos 50 GB de SSD local                                              | Não será atrasado pelo Cluster Service Fabric           | Não pode ser atrasada por nenhum período de tempo significativo                                                    |

> [!WARNING]
> Os tipos de nó em execução com durabilidade bronze não obtêm _nenhum privilégio_. Isso significa que os trabalhos de infraestrutura que afetam suas cargas de trabalho sem estado não serão interrompidos ou atrasados, o que pode afetar suas cargas de trabalho. Use somente bronze para tipos de nós que executam apenas cargas de trabalho sem estado. Para cargas de trabalho de produção, é recomendável executar prata ou superior. 
> 
> Independentemente de qualquer nível de durabilidade, a operação de desalocação no conjunto de dimensionamento de VM destruirá o cluster [](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate)

**Vantagens de usar os níveis de durabilidade prata ou ouro**
 
- Reduz o número de etapas necessárias em uma operação de redução horizontal (ou seja, a desativação do nó e remove-ServiceFabricNodeState é chamada automaticamente).
- Reduz o risco de perda de dados devido a uma operação de alteração de SKU de VM in-loco iniciada pelo cliente ou operações de infraestrutura do Azure.

**Desvantagens de usar os níveis de durabilidade prata ou ouro**
 
- As implantações no conjunto de dimensionamento de máquinas virtuais e outros recursos do Azure relacionados podem ser atrasadas, podem atingir o tempo limite ou podem ser totalmente bloqueadas por problemas no cluster ou no nível de infraestrutura. 
- Aumenta o número de [eventos de ciclo de vida de réplica](service-fabric-reliable-services-lifecycle.md) (por exemplo, trocas primárias) devido a desativações de nó automatizadas durante operações de infraestrutura do Azure.
- Retira os nós do serviço por períodos de tempo enquanto as atividades de manutenção de hardware ou atualizações de software da plataforma do Azure estão ocorrendo. Você pode ver nós com status desabilitando/desabilitado durante essas atividades. Isso reduz a capacidade do cluster temporariamente, mas não deve afetar a disponibilidade do cluster ou dos aplicativos.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Recomendações para quando usar os níveis de durabilidade prata ou ouro

Use a durabilidade Silver ou ouro para todos os tipos de nós que hospedam serviços com estado que você espera expandir (reduzir a contagem de instâncias de VM) com frequência e preferir que as operações de implantação sejam atrasadas e que a capacidade seja reduzida em favor de simplificar essas escalações das. Os cenários de expansão (adicionando instâncias de VMs) não são executados em sua escolha da camada de durabilidade, mas apenas o dimensionamento.

### <a name="changing-durability-levels"></a>Alterando os níveis de durabilidade
- Os tipos de nó com níveis de durabilidade de prata ou ouro não podem ser desatualizados para bronze.
- A atualização de bronze para prata ou Gold pode levar algumas horas.
- Ao alterar o nível de durabilidade, atualize-o na configuração de extensão de Service Fabric em seu recurso de conjunto de dimensionamento de máquinas virtuais e na definição de tipo de nó no recurso de Cluster Service Fabric. Esses valores devem corresponder.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendações operacionais para o tipo de nó que você definiu para o nível de durabilidade prata ou ouro.

- Mantenha o cluster e os aplicativos sempre íntegros e verifique se os aplicativos respondem a todos os [eventos de ciclo de vida de réplica de serviço](service-fabric-reliable-services-lifecycle.md) (como a réplica na compilação está paralisada) em tempo hábil.
- Adote maneiras mais seguras de fazer uma alteração de SKU de VM (escalar verticalmente): Alterar o SKU da VM de um conjunto de dimensionamento de máquinas virtuais requer várias etapas e considerações. Este é o processo que você pode seguir para evitar problemas comuns.
    - **Para tipos de nós não primários:** É recomendável que você crie um novo conjunto de dimensionamento de máquinas virtuais, modifique a restrição de posicionamento do serviço para incluir o novo tipo de nó/conjunto de dimensionamento de máquinas virtuais e, em seguida, reduza a contagem de instância antiga do conjunto de dimensionamento de máquinas virtuais para zero, um nó por vez (isso é para fazer Certifique-se de que a remoção dos nós não afete a confiabilidade do cluster).
    - **Para o tipo de nó primário:** Se o SKU da VM selecionado estiver na capacidade e você quiser alterar para um SKU de VM maior, siga nossas diretrizes sobre [o dimensionamento vertical de um tipo de nó primário](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Mantenha uma contagem mínima de cinco nós para qualquer conjunto de dimensionamento de máquinas virtuais que tenha o nível de durabilidade Gold ou prata habilitado.
- Cada conjunto de dimensionamento de máquinas virtuais com o nível de durabilidade prata ou ouro deve mapear para seu próprio tipo de nó no cluster de Service Fabric. O mapeamento de vários conjuntos de dimensionamento de máquinas virtuais para um único tipo de nó impedirá a coordenação entre o Cluster Service Fabric e a infraestrutura do Azure de funcionar corretamente.
- Não exclua instâncias de VM aleatórias, sempre use o recurso de redução do conjunto de dimensionamento de máquinas virtuais. A exclusão de instâncias de VM aleatórias tem um potencial de criar desequilíbrios na instância de VM espalhada por UD e FD. Esse desequilíbrio pode afetar negativamente a capacidade dos sistemas de balancear a carga corretamente entre as instâncias de serviço/réplicas de serviço.
- Se estiver usando o dimensionamento automático, defina as regras de forma que a escala em (removendo instâncias de VM) seja feita apenas em um nó por vez. Reduzir mais de uma instância por vez não é seguro.
- Se você excluir ou desalocar VMs no tipo de nó primário, nunca deverá reduzir a contagem de VMs alocadas abaixo do que a camada de confiabilidade requer. Essas operações serão bloqueadas indefinidamente em um conjunto de dimensionamento com um nível de durabilidade de prata ou ouro.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de confiabilidade do cluster
A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar neste cluster no tipo de nó primário. Quanto mais o número de réplicas, mais confiável os serviços do sistema estão no cluster.  

A camada de confiabilidade pode ter os seguintes valores:

* Platina-execute os serviços do sistema com uma contagem de conjuntos de réplicas de destino de sete
* Ouro – executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de sete
* Prata-execute os serviços do sistema com uma contagem de conjuntos de réplicas de destino de cinco 
* Bronze-executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de três

> [!NOTE]
> A camada de confiabilidade que você escolhe determina o número mínimo de nós que seu tipo de nó primário deve ter. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Recomendações para a camada de confiabilidade

Ao aumentar ou diminuir o tamanho do cluster (a soma de instâncias de VM em todos os tipos de nó), você deve atualizar a confiabilidade do cluster de uma camada para outra. Isso dispara as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas dos serviços do sistema. Aguarde a conclusão da atualização em andamento antes de fazer outras alterações no cluster, como adicionar nós.  Você pode monitorar o progresso da atualização na Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Aqui está a recomendação sobre como escolher a camada de confiabilidade.  O número de nós de semente também é definido como o número mínimo de nós para uma camada de confiabilidade.  Por exemplo, para um cluster com confiabilidade Gold, há sete nós de semente.

| **Número de nós de cluster** | **Camada de confiabilidade** |
| --- | --- |
| 1 |Não especifique o parâmetro de camada de confiabilidade, o sistema o calcula |
| 3 |Bronze |
| 5 ou 6|Prateado |
| 7 ou 8 |Dourado |
| 9 e acima |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó primário-diretrizes de capacidade

Aqui está a orientação para planejar a capacidade do tipo de nó primário:

- **Número de instâncias de VM para executar qualquer carga de trabalho de produção no Azure:** Você deve especificar um tamanho mínimo de tipo de nó primário de 5 e uma camada de confiabilidade de prata.  
- **Número de instâncias de VM para executar cargas de trabalho de teste no Azure** Você pode especificar um tamanho mínimo de tipo de nó primário de 1 ou 3. O cluster de um nó é executado com uma configuração especial e, portanto, não há suporte para a expansão desse cluster. O cluster de um nó, não tem confiabilidade e, portanto, no modelo do Resource Manager, você precisa remover/não especificar que a configuração (não definir o valor de configuração não é suficiente). Se você configurar o cluster de um nó configurado por meio do portal, a configuração será automaticamente manipulada. Não há suporte para clusters de um e três nós para executar cargas de trabalho de produção. 
- **SKU DA VM:** O tipo de nó primário é onde os serviços do sistema são executados, portanto, o SKU de VM que você escolher para ele deve levar em conta a carga de pico geral que você planeja colocar no cluster. Aqui está uma analogia para ilustrar o que quero dizer aqui – imagine o tipo de nó primário como "pulmões", é o que fornece oxigênio para o cérebro e, portanto, se o cérebro não obtiver oxigênio suficiente, seu corpo será afetado. 

Como as necessidades de capacidade de um cluster são determinadas pela carga de trabalho que você planeja executar no cluster, não podemos fornecer a você uma orientação qualitativa para sua carga de trabalho específica. no entanto, aqui estão as diretrizes amplas para ajudá-lo a começar

Para cargas de trabalho de produção: 

- É recomendável dedicar seus clusters primários NodeType aos serviços do sistema e usar restrições de posicionamento para implantar seu aplicativo em NodeTypes secundários.
- O SKU de VM recomendado é o padrão D2_V2 ou equivalente com um mínimo de 50 GB de SSD local.
- O SKU de VM de uso mínimo com suporte é Standard_D2_V3 ou Standard D1_V2 ou equivalente com um mínimo de 50 GB de SSD local. 
- Nossa recomendação é um mínimo de 50 GB. Para suas cargas de trabalho, especialmente ao executar contêineres do Windows, discos maiores são necessários. 
- SKUs de VM de núcleo parcial, como Standard a0, não têm suporte para cargas de trabalho de produção.
- Não há suporte para SKUs de VM de série para cargas de trabalho de produção por motivos de desempenho.
- Não há suporte para VMs de baixa prioridade.

> [!WARNING]
> Alterar o tamanho de SKU da VM do nó primário em um cluster em execução é uma operação de dimensionamento e documentado na documentação do [scale out do conjunto de dimensionamento de máquinas virtuais](virtual-machine-scale-set-scale-node-type-scale-out.md) .

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo de nó não primário-diretrizes de capacidade para cargas de trabalho com estado

Esta diretriz é para cargas de trabalho com estado usando [coleções confiáveis](service-fabric-choose-framework.md) do Service Fabric ou Reliable Actors que você está executando no tipo de nó não primário.

**Número de instâncias de VM:** Para cargas de trabalho de produção com monitoração de estado, é recomendável executá-las com uma contagem de réplicas mínima e de destino de 5. Isso significa que, em um estado estável, você acaba com uma réplica (de um conjunto de réplicas) em cada domínio de falha e domínio de atualização. O conceito de nível de confiabilidade total para o tipo de nó primário é uma maneira de especificar essa configuração para serviços do sistema. Portanto, a mesma consideração também se aplica aos seus serviços com estado.

Portanto, para cargas de trabalho de produção, o tamanho mínimo recomendado de tipo de nó não primário é 5, se você estiver executando cargas de trabalho com estado.

**SKU DA VM:** Esse é o tipo de nó em que os serviços de aplicativo estão em execução, portanto, a SKU de VM que você escolher para ele deve levar em conta a carga de pico que você planeja colocar em cada nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que você planeja executar no cluster, portanto, não podemos fornecer a você uma orientação qualitativa para sua carga de trabalho específica. no entanto, aqui estão as diretrizes amplas para ajudá-lo a começar

Para cargas de trabalho de produção 

- O SKU de VM recomendado é o padrão D2_V2 ou equivalente com um mínimo de 50 GB de SSD local.
- O SKU de VM de uso mínimo com suporte é Standard_D2_V3 ou Standard D1_V2 ou equivalente com um mínimo de 50 GB de SSD local. 
- SKUs de VM de núcleo parcial, como Standard a0, não têm suporte para cargas de trabalho de produção.
- Não há suporte para SKUs de VM de série para cargas de trabalho de produção por motivos de desempenho.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo de nó não primário-diretrizes de capacidade para cargas de trabalho sem estado

Essas diretrizes de cargas de trabalho sem estado que você está executando no tipo de nó não primário.

**Número de instâncias de VM:** Para cargas de trabalho de produção sem monitoração de estado, o tamanho mínimo de tipo de nó não primário com suporte é 2. Isso permite que você execute duas instâncias sem estado de seu aplicativo e permita que seu serviço sobreviver à perda de uma instância de VM. 

**SKU DA VM:** Esse é o tipo de nó em que os serviços de aplicativo estão em execução, portanto, a SKU de VM que você escolher para ele deve levar em conta a carga de pico que você planeja colocar em cada nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que você planeja executar no cluster. Não podemos fornecer a você diretrizes qualitativas para sua carga de trabalho específica.  No entanto, aqui estão as diretrizes amplas para ajudá-lo a começar.

Para cargas de trabalho de produção 

- O SKU de VM recomendado é o padrão D2_V2 ou equivalente. 
- O SKU de VM de uso mínimo com suporte é o padrão D1 ou padrão D1_V2 ou equivalente. 
- SKUs de VM de núcleo parcial, como Standard a0, não têm suporte para cargas de trabalho de produção.
- Não há suporte para SKUs de VM de série para cargas de trabalho de produção por motivos de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passos seguintes
Depois de concluir o planejamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança de Cluster Service Fabric](service-fabric-cluster-security.md)
* [Dimensionamento de Cluster Service Fabric](service-fabric-cluster-scaling.md)
* [Planejamento de recuperação de desastre](service-fabric-disaster-recovery.md)
* [Relação de NodeTypes ao conjunto de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
