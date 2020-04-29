---
title: Dimensionamento do cluster azure service fabric
description: Aprenda sobre a escala de clusters azure service fabric dentro ou fora e para cima ou para baixo. Como a aplicação exige mudanças, também os clusters de Tecido de Serviço.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258697"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Clusters de tecido de serviço Azure de escala
Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamado de nó. Os aglomerados podem conter potencialmente milhares de nós. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Por que escalar o aglomerado? A aplicação exige mudanças ao longo do tempo.  Pode ser necessário aumentar os recursos de cluster para satisfazer o aumento da carga de trabalho das aplicações ou o tráfego de rede ou diminuir os recursos de cluster quando a procura diminui.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Escalando para dentro e para fora, ou escalação horizontal
Altera o número de nós no cluster.  Assim que os novos nós se juntam ao cluster, o [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) transfere os serviços para eles, o que reduz a carga nos nós existentes.  Também pode diminuir o número de nós se os recursos do cluster não estiverem a ser utilizados de forma eficiente.  À medida que os nós saem do cluster, os serviços saem desses nós e a carga aumenta nos restantes nós.  Reduzir o número de nós num cluster em funcionamento em Azure pode economizar dinheiro, uma vez que paga pelo número de VMs que utiliza e não pela carga de trabalho nesses VMs.  

- Vantagens: Escala infinita, em teoria.  Se a sua aplicação for concebida para a escalabilidade, pode permitir um crescimento ilimitado adicionando mais nós.  A ferramenta em ambientes de nuvem facilita a adição ou remoção de nós, por isso é fácil ajustar a capacidade e só paga pelos recursos que utiliza.  
- Desvantagens: As aplicações devem ser [concebidas para a escalabilidade](service-fabric-concepts-scalability.md).  As bases de dados de aplicações e a persistência podem exigir também trabalhos arquitetónicos adicionais à escala.  No entanto, as [coleções fiáveis](service-fabric-reliable-services-reliable-collections.md) em serviços de serviço saem, no entanto, facilitam muito a escala dos dados da sua aplicação.

Os conjuntos de escala de máquinavirtual são um recurso de computação Azure que pode usar para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster Azure é [configurado como um conjunto](service-fabric-cluster-nodetypes.md)de escala separado . Cada tipo de nó pode então ser escalado dentro ou fora de forma independente, ter diferentes conjuntos de portas abertas, e pode ter métricas de capacidade diferentes. 

Ao escalonar um cluster Azure, tenha em mente as seguintes orientações:
- os tipos primários de nós que executam cargas de trabalho de produção devem ter sempre cinco ou mais nós.
- Os tipos de nó não primários que executam cargas horárias de produção audais devem ter sempre cinco ou mais nós.
- Os tipos não primários de nó que executam cargas de trabalho apátridas de produção apátridas devem sempre ter dois ou mais nós.
- Qualquer nó de nível de [durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Ouro ou Prata deve ter sempre cinco ou mais nós.
- Não remova as instâncias/nós de VM aleatórios de um tipo de nó, utilize sempre a função de escala de escala de máquina virtual. A eliminação de instâncias vm aleatórias pode afetar negativamente a capacidade dos sistemas de carregar corretamente o equilíbrio.
- Se utilizar regras de escala automática, estabeleça as regras para que a escala (remoção das instâncias VM) seja feita com um nó de cada vez. Reduzir mais do que um caso de cada vez não é seguro.

Uma vez que os tipos de nó de tecido de serviço no seu cluster são compostos por conjuntos de escala de máquina virtual na extremidade traseira, pode [configurar regras de escala automática ou escalar manualmente](service-fabric-cluster-scale-up-down.md) cada conjunto de escala de tipo de nó/máquina virtual.

### <a name="programmatic-scaling"></a>Escala programática
Em muitos cenários, [escalar um cluster manualmente ou com regras](service-fabric-cluster-scale-up-down.md) de escala automática são boas soluções. Para cenários mais avançados, no entanto, eles podem não ser o ajuste certo. As potenciais desvantagens destas abordagens incluem:

- A escalação manual requer que você assine e solicite explicitamente operações de escala. Se forem necessárias operações de escala frequenteou em momentos imprevisíveis, esta abordagem pode não ser uma boa solução.
- Quando as regras de escala automática removem uma instância de um conjunto de escala de máquina virtual, não removem automaticamente o conhecimento desse nó do cluster de tecido de serviço associado, a menos que o tipo de nó tenha um nível de durabilidade de Prata ou Ouro. Como as regras de escala automática funcionam ao nível definido pela escala (e não ao nível do Tecido de Serviço), as regras de escala automática podem remover os nódosos de Tecido de Serviço sem os desligar graciosamente. Esta remoção rude do nó deixará o nó de 'ghost' Service Fabric para trás após operações de escala. Um indivíduo (ou um serviço) teria de limpar periodicamente o estado do nó removido no cluster Service Fabric.
- Um nó com um nível de durabilidade de Ouro ou Prata limpa automaticamente os nós removidos, pelo que não é necessária limpeza adicional.
- Embora existam [muitas métricas](../azure-monitor/platform/autoscale-common-metrics.md) suportadas por regras de escala automática, ainda é um conjunto limitado. Se o seu cenário requer escala com base em alguma métrica não abrangida nesse conjunto, então as regras de escala automática podem não ser uma boa opção.

A forma como deve aproximar-se da escala de tecido de serviço depende do seu cenário. Se a escala for incomum, a capacidade de adicionar ou remover os nós manualmente é provavelmente suficiente. Para cenários mais complexos, regras de escala automática e SDKs expondo a capacidade de escalar programáticamente oferecem alternativas poderosas.

Existem APIs azure que permitem que as aplicações trabalhem programáticamente com conjuntos de escala de máquina virtual e clusters de tecido de serviço. Se as opções de escala automática existentes não funcionarem para o seu cenário, estas APIs tornam possível implementar uma lógica de escala personalizada. 

Uma abordagem para implementar esta funcionalidade de auto-escalação "caseira" é adicionar um novo serviço apátrida à aplicação Service Fabric para gerir operações de escala. A criação do seu próprio serviço de escala proporciona o mais alto grau de controlo e personalizabilidade sobre o comportamento de escala da sua aplicação. Isto pode ser útil para cenários que requerem um controlo preciso sobre quando ou como uma aplicação entra ou sai. No entanto, este controlo vem com uma troca de complexidade de código. Usar esta abordagem significa que é necessário possuir um código de escala, o que não é trivial. Dentro do método `RunAsync` do serviço, um conjunto de gatilhos pode determinar se é necessária escala (incluindo parâmetros de verificação, tais como o tamanho máximo do cluster e arrefecimento de escala).   

A API utilizada para interações de conjuntos de máquinas virtuais (tanto para verificar o número atual de casos de máquinas virtuais como para modificá-la) é a [fluente biblioteca azure Management Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A fluente biblioteca computacional fornece uma API de fácil utilização para interagir com conjuntos de escala de máquinas virtuais.  Para interagir com o próprio cluster de tecido de serviço, utilize [system.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

O código de escala não precisa de funcionar como um serviço no cluster para ser escalado, no entanto. Ambos `IAzure` `FabricClient` podem ligar-se remotamente aos seus recursos Azure associados, pelo que o serviço de escalação pode facilmente ser uma aplicação de consola ou um serviço Windows que funciona fora da aplicação Service Fabric.

Com base nestas limitações, poderá desejar [implementar modelos de escala automática mais personalizados.](service-fabric-cluster-programmatic-scaling.md)

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalando para cima e para baixo, ou escalação vertical 
Altera os recursos (CPU, memória ou armazenamento) dos nós do cluster.
- Vantagens: A arquitetura de software e aplicação permanece a mesma.
- Desvantagens: Escala finita, uma vez que há um limite para o quanto pode aumentar os recursos em nós individuais. Tempo de inatividade, porque terá de desligar as máquinas físicas ou virtuais para adicionar ou remover recursos.

Os conjuntos de escala de máquinavirtual são um recurso de computação Azure que pode usar para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster Azure é [configurado como um conjunto](service-fabric-cluster-nodetypes.md)de escala separado . Cada tipo de nó pode então ser gerido separadamente.  Escalar um nó para cima ou para baixo envolve alterar o SKU das instâncias da máquina virtual no conjunto de escala. 

> [!WARNING]
> Recomendamos que não altere o VM SKU de um tipo de conjunto/nó de escala, a menos que esteja a funcionar na [durabilidade da Prata ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Mudar o tamanho VM SKU é uma operação de infraestrutura destrutiva em dados. Sem alguma capacidade de atrasar ou monitorizar esta alteração, é possível que a operação possa causar perda de dados para serviços estatais ou causar outras questões operacionais imprevistas, mesmo para cargas de trabalho apátridas. 
>

Ao escalonar um cluster Azure, tenha em mente a seguinte orientação:
- Se escalonar um nó primário, nunca deve escaloná-lo mais do que o nível de [fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) permite.

O processo de escalonamento de um nó para cima ou para baixo é diferente dependendo se é um nó não primário ou primário.

### <a name="scaling-non-primary-node-types"></a>Tipos de nó não primário salcados
Crie um novo tipo de nó com os recursos de que necessita.  Atualize os constrangimentos de colocação dos serviços de funcionamento para incluir o novo tipo de nó.  Gradualmente (um de cada vez), reduza a contagem de exemplos da antiga instância do nó para zero para que a fiabilidade do cluster não seja afetada.  Os serviços migrarão gradualmente para o novo tipo de nó, uma vez que o tipo de nó antigo é desativado.

### <a name="scaling-the-primary-node-type"></a>Escalando o tipo de nó primário
Recomendamos que não altere o VM SKU do tipo de nó primário. Se precisar de mais capacidade de cluster, recomendamos adicionar mais instâncias. 

Se isso não for possível, pode criar um novo cluster e restaurar o estado de [aplicação](service-fabric-reliable-services-backup-restore.md) (se aplicável) a partir do seu antigo cluster. Não precisa de restaurar nenhum estado de serviço do sistema, eles são recriados quando você implementa as suas aplicações para o seu novo cluster. Se estava apenas a executar aplicações apátridas no seu cluster, então tudo o que faz é implementar as suas aplicações para o novo cluster, não tem nada para restaurar. Se decidir seguir a rota não suportada e quiser alterar o VM SKU, então faça modificações na definição de modelo de modelo de escala de máquina virtual para refletir o novo SKU. Se o seu cluster tiver apenas um tipo de nó, certifique-se de que todas as suas aplicações imponentes respondem a todos os [eventos](service-fabric-reliable-services-lifecycle.md) de ciclo de vida de réplica de serviço (como réplica em construção está preso) em tempo útil e que a duração da reconstrução da réplica de serviço é inferior a cinco minutos (para o nível de durabilidade da Prata). 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escala um aglomerado Azure para dentro ou para fora.](service-fabric-tutorial-scale-cluster.md)
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) utilizando o fluente sdk de computação Azure.
* [Escala um aglomerado autónomo dentro ou fora.](service-fabric-cluster-windows-server-add-remove-nodes.md)

