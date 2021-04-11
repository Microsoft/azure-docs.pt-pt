---
title: Escala de cluster de tecido de serviço Azure
description: Saiba mais sobre a escala de clusters de tecidos de serviço Azure dentro ou fora e para cima ou para baixo. Como a aplicação exige mudança, também os clusters de Tecido de Serviço.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 610c43f64f9073aefe8008473209039122cf36d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591785"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Aglomerados de tecido de serviço de escala Azure
Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamada de nó. Os aglomerados podem conter potencialmente milhares de nós. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Por que escalar o aglomerado? A aplicação exige mudanças ao longo do tempo.  Poderá ser necessário aumentar os recursos de cluster para responder ao aumento da carga de trabalho de aplicação ou ao tráfego de redes ou diminuir os recursos de cluster quando a procura diminui.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Escalonamento para dentro e para fora, ou escala horizontal
Altera o número de nós no cluster.  Assim que os novos nós se juntam ao cluster, o [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) transfere-lhes serviços que reduzem a carga nos nós existentes.  Também pode diminuir o número de nós se os recursos do cluster não estiverem a ser utilizados de forma eficiente.  À medida que os nós saem do cluster, os serviços movem-se desses nós e aumentam os aumentos de carga nos restantes nós.  A redução do número de nós num cluster em execução em Azure pode economizar dinheiro, uma vez que paga pelo número de VMs que utiliza e não pela carga de trabalho nesses VMs.  

- Vantagens: Escala infinita, em teoria.  Se a sua aplicação for concebida para a escalabilidade, pode permitir um crescimento ilimitado adicionando mais nós.  A ferramenta em ambientes em nuvem facilita a adição ou remoção de nós, por isso é fácil ajustar a capacidade e só paga pelos recursos que utiliza.  
- Desvantagens: As aplicações devem ser [concebidas para a escalabilidade](service-fabric-concepts-scalability.md).  As bases de dados de aplicações e a persistência podem exigir trabalhos arquitetónicos adicionais à escala também.  No entanto, [as coleções fiáveis](service-fabric-reliable-services-reliable-collections.md) nos serviços estatais da Service Fabric tornam muito mais fácil a escala dos dados da sua aplicação.

Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que pode utilizar para implantar e gerir uma coleção de máquinas virtuais como conjunto. Todos os tipos de nó definidos num cluster Azure [são configurado como um conjunto de escala separada](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser dimensionado dentro ou fora de forma independente, ter diferentes conjuntos de portas abertas, e pode ter métricas de capacidade diferentes. 

Ao escalar um cluster Azure, tenha em mente as seguintes diretrizes:
- os tipos de nó primário que executam cargas de trabalho de produção devem ter sempre cinco ou mais nós.
- os tipos de nó não primário que executam cargas de trabalho de produção estatais devem ter sempre cinco ou mais nós.
- Os tipos de nó não primário que executam cargas de trabalho de produção apátridas devem ter sempre dois ou mais nós.
- Qualquer tipo de nó de [durabilidade](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de Ouro ou Prata deve ter sempre cinco ou mais nós.
- Não remova as instâncias/nós VM aleatórios de um tipo de nó, utilize sempre a balança de escala de máquina virtual em característica. A supressão de instâncias VM aleatórias pode afetar negativamente a capacidade dos sistemas de carregar corretamente o equilíbrio.
- Se utilizar regras de autoescalação, desajuste as regras de modo a que a escala (remoção de instâncias VM) seja feita um nó de cada vez. Reduzir mais de um caso de cada vez não é seguro.

Uma vez que os tipos de nó de tecido de serviço no seu cluster são compostos por conjuntos de balança de máquinas virtuais no backend, pode [configurar regras de escala automática ou escalar manualmente](service-fabric-cluster-scale-in-out.md) cada conjunto de escala tipo de nó/máquina virtual.

### <a name="programmatic-scaling"></a>Escala programática
Em muitos cenários, [escalar um cluster manualmente ou com regras de autoescala](service-fabric-cluster-scale-in-out.md) são boas soluções. Para cenários mais avançados, no entanto, podem não ser o ajuste certo. Os inconvenientes potenciais para estas abordagens incluem:

- O dimensionamento manual requer que faça sing e solicite explicitamente operações de escala. Se as operações de escalonamento forem necessárias frequentemente ou em momentos imprevisíveis, esta abordagem pode não ser uma boa solução.
- Quando as regras de escala automática removem uma instância de um conjunto de escala de máquina virtual, não removem automaticamente o conhecimento desse nó do cluster de tecido de serviço associado, a menos que o tipo de nó tenha um nível de durabilidade de Prata ou Ouro. Dado que as regras de escala automática funcionam ao nível definido na escala (e não ao nível do Tecido de Serviço), as regras de escala automática podem remover os nós do Tecido de Serviço sem os desligar graciosamente. Esta remoção rude do nó deixará o nó de nó de tecido de serviço 'fantasma' para trás após operações de escala. Um indivíduo (ou um serviço) teria de limpar periodicamente o estado do nó removido no cluster de Tecido de Serviço.
- Um tipo de nó com um nível de durabilidade de Ouro ou Prata limpa automaticamente os nós removidos, para que não seja necessária uma limpeza adicional.
- Embora existam [muitas métricas](../azure-monitor/autoscale/autoscale-common-metrics.md) suportadas por regras de escala automática, ainda é um conjunto limitado. Se o seu cenário requer uma escala com base em alguma métrica não abrangida por esse conjunto, então as regras de escala automática podem não ser uma boa opção.

A forma como deve abordar o dimensionamento do Tecido de Serviço depende do seu cenário. Se a escala é incomum, a capacidade de adicionar ou remover os nós manualmente é provavelmente suficiente. Para cenários mais complexos, regras de escala automática e SDKs expondo a capacidade de escalar programáticamente oferecem alternativas poderosas.

Existem APIs Azure que permitem que as aplicações funcionem programáticamente com conjuntos de escala de máquinas virtuais e clusters de Tecido de Serviço. Se as opções de escala automática existentes não funcionarem para o seu cenário, estas APIs tornam possível implementar uma lógica de escala personalizada. 

Uma abordagem para implementar esta funcionalidade de auto-escalação "caseira" é adicionar um novo serviço apátrida à aplicação Service Fabric para gerir as operações de escala. A criação do seu próprio serviço de escala proporciona o mais alto grau de controlo e personalização sobre o comportamento de escala da sua aplicação. Isto pode ser útil para cenários que requerem controlo preciso sobre quando ou como uma aplicação escala dentro ou fora. No entanto, este controlo vem com uma compensação da complexidade do código. A utilização desta abordagem significa que é necessário possuir um código de escala, que não é trivial. Dentro do método do `RunAsync` serviço, um conjunto de gatilhos pode determinar se é necessário escalonamento (incluindo parâmetros de verificação, tais como o tamanho máximo do cluster e arrefecimento de escala).   

A API utilizada para interações de conjunto de escalas de máquinas virtuais (tanto para verificar o número atual de casos de máquinas virtuais como para modificá-la) é a [fluente biblioteca Azure Management Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A biblioteca de computação fluente fornece uma API fácil de usar para interagir com conjuntos de escala de máquina virtual.  Para interagir com o próprio cluster de tecido de serviço, utilize [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

O código de escala não precisa de funcionar como um serviço no cluster para ser dimensionado, no entanto. Ambos `IAzure` e `FabricClient` podem ligar-se remotamente aos seus recursos Azure associados, pelo que o serviço de escala pode facilmente ser uma aplicação de consola ou serviço Windows que funciona a partir de fora da aplicação Service Fabric.

Com base nestas limitações, poderá pretender [implementar modelos de escala automática mais personalizados.](service-fabric-cluster-programmatic-scaling.md)

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalonamento para cima e para baixo, ou escala vertical 
Altera os recursos (CPU, memória ou armazenamento) dos nós no cluster.
- Vantagens: A arquitetura de software e aplicações permanece a mesma.
- Desvantagens: Escala finita, uma vez que há um limite para quanto você pode aumentar recursos em nós individuais. Tempo de inatividade, porque terá de desligar as máquinas físicas ou virtuais para adicionar ou remover recursos.

Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que pode utilizar para implantar e gerir uma coleção de máquinas virtuais como conjunto. Todos os tipos de nó definidos num cluster Azure [são configurado como um conjunto de escala separada](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser gerido separadamente.  Escalonar um nó para cima ou para baixo envolve a adição de um novo tipo de nó (com VM SKU atualizado) e a remoção do antigo tipo de nó.

Ao escalar um cluster Azure, tenha em mente a seguinte orientação:
- Se reduzir um tipo de nó primário, nunca deve escaloná-lo mais do que o [nível de fiabilidade](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) permite.

O processo de escalonamento de um nó para cima ou para baixo é diferente dependendo se é um tipo de nó não primário ou primário.

### <a name="scaling-non-primary-node-types"></a>Escalonamento de tipos de nó não primários
Crie um novo tipo de nó com os recursos necessários.  Atualize as restrições de colocação dos serviços de execução para incluir o novo tipo de nó.  Gradualmente (um de cada vez), reduza a contagem de instâncias da contagem de instâncias do antigo nó para zero de modo a que a fiabilidade do cluster não seja afetada.  Os serviços migrarão gradualmente para o novo tipo de nó, uma vez que o antigo tipo de nó é desativado.

### <a name="scaling-the-primary-node-type"></a>Escalonamento do tipo de nó primário
Implementar um novo tipo de nó primário com VM SKU atualizado e, em seguida, desativar as instâncias originais do tipo nó primário uma de cada vez para que os serviços do sistema migram para o conjunto de escala nova. Verifique se o cluster e os novos nós estão saudáveis e, em seguida, remova o conjunto de escala original e o estado do nó para os nós eliminados.

Se isso não for possível, pode criar um novo cluster e restaurar o estado de [aplicação](service-fabric-reliable-services-backup-restore.md) (se aplicável) a partir do seu antigo cluster. Não precisa de restaurar nenhum estado de serviço do sistema, são recriados quando implementa as suas aplicações no seu novo cluster. Se estavas a executar aplicações apátridas no teu cluster, então tudo o que fazes é implantar as tuas aplicações no novo cluster, não tens nada para restaurar.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escalar um aglomerado Azure dentro ou fora](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) usando o fluente Azure compute SDK.
* [Escalar um aglomerado autónomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

