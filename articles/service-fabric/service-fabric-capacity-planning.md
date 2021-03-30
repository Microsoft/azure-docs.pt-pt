---
title: Planeamento de capacidade para apps de tecido de serviço
description: Descreve como identificar o número de nós computacional necessários para uma aplicação de Tecido de Serviço
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75377213"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planeamento de capacidade para aplicações de Tecido de Serviço
Este documento ensina-lhe como estimar a quantidade de recursos (CPUs, RAM, armazenamento de discos) que precisa para executar as suas aplicações Azure Service Fabric. É comum que os seus requisitos de recursos mudem ao longo do tempo. Normalmente, necessita de poucos recursos à medida que desenvolve/testa o seu serviço, e depois requer mais recursos à medida que entra em produção e a sua aplicação cresce em popularidade. Ao conceber a sua aplicação, pense nos requisitos de longo prazo e faça escolhas que permitam que o seu serviço se dimensione para satisfazer a elevada procura do cliente.

 Quando cria um cluster de Tecido de Serviço, decide-se que tipo de máquinas virtuais (VMs) compõem o cluster. Cada VM vem com uma quantidade limitada de recursos sob a forma de CPUs (núcleos e velocidade), largura de banda de rede, RAM e armazenamento de discos. À medida que o seu serviço cresce ao longo do tempo, pode fazer upgrade para VMs que oferecem maiores recursos e/ou adicionar mais VMs ao seu cluster. Para fazer este último, você deve arquitetar o seu serviço inicialmente para que possa aproveitar os novos VMs que são adicionados dinamicamente ao cluster.

Alguns serviços gerem pouco ou nenhum dado sobre os próprios VMs. Por conseguinte, o planeamento da capacidade destes serviços deve centrar-se principalmente no desempenho, o que significa selecionar os CPUs apropriados (núcleos e velocidade) dos VMs. Além disso, deve considerar a largura de banda da rede, incluindo a frequência com que estão a ocorrer transferências de rede e a quantidade de dados que estão a ser transferidos. Se o seu serviço precisar de executar bem como aumentar o uso do serviço, pode adicionar mais VMs ao cluster e equilibrar os pedidos de rede em todos os VMs.

Para os serviços que gerem grandes quantidades de dados sobre os VMs, o planeamento da capacidade deve centrar-se principalmente na dimensão. Assim, deve considerar cuidadosamente a capacidade da RAM e do armazenamento em disco da VM. O sistema de gestão de memória virtual no Windows faz com que o espaço do disco pareça RAM para código de aplicação. Além disso, o tempo de funcionamento do Tecido de Serviço fornece uma visualização inteligente mantendo apenas dados quentes na memória e movendo os dados frios para o disco. As aplicações podem assim utilizar mais memória do que está fisicamente disponível no VM. Ter mais RAM simplesmente aumenta o desempenho, uma vez que o VM pode manter mais armazenamento em disco na RAM. O VM que seleciona deve ter um disco suficientemente grande para armazenar os dados que pretende no VM. Da mesma forma, o VM deve ter RAM suficiente para lhe proporcionar o desempenho que deseja. Se os dados do seu serviço crescerem ao longo do tempo, pode adicionar mais VMs ao cluster e dividir os dados em todos os VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determine quantos nos acenos precisa
A partilha do seu serviço permite-lhe aumentar os dados do seu serviço. Para obter mais informações sobre a partição, consulte [o Tecido de Serviço de Partição.](service-fabric-concepts-partitioning.md) Cada divisória deve caber dentro de um único VM, mas várias divisórias (pequenas) podem ser colocadas num único VM. Assim, ter mais pequenas divisórias dá-lhe maior flexibilidade do que ter algumas divisórias maiores. A compensação é que ter muitas divisórias aumenta a sobrecarga do Service Fabric e não é possível realizar operações transacionadas através de divisórias. Existe também mais tráfego de rede potencial se o seu código de serviço precisar frequentemente de aceder a peças de dados que vivem em diferentes divisórias. Ao conceber o seu serviço, deve considerar cuidadosamente estes prós e contras para chegar a uma estratégia eficaz de partição.

Vamos assumir que a sua aplicação tem um único serviço imponente que tem um tamanho de loja que espera crescer para DB_Size GB num ano. Está disposto a adicionar mais aplicações (e divisórias) à medida que experimenta crescimento para além desse ano.  O fator de replicação (RF), que determina o número de réplicas para o seu serviço, tem impacto no total DB_Size. O total DB_Size em todas as réplicas é o Fator de Replicação multiplicado por DB_Size.  Node_Size representa o espaço do disco/RAM por nó que pretende utilizar para o seu serviço. Para um melhor desempenho, o DB_Size deve caber na memória em todo o cluster, e uma Node_Size que está em torno da RAM do VM deve ser escolhida. Ao atribuir uma Node_Size maior do que a capacidade de RAM, está a contar com a paging fornecida pelo tempo de funcionamento do Service Fabric. Assim, o seu desempenho pode não ser o ideal se todos os seus dados forem considerados quentes (desde então os dados são paged in/out). No entanto, para muitos serviços onde apenas uma fração dos dados é quente, é mais rentável.

O número de nós necessários para o desempenho máximo pode ser calculado da seguinte forma:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Contabilização do crescimento
Pode querer calcular o número de nós com base no DB_Size a que espera que o seu serviço cresça, para além da DB_Size com que começou. Em seguida, aumente o número de nós à medida que o seu serviço cresce para que não esteja a exagerar no número de nós. Mas o número de divisórias deve basear-se no número de nós necessários quando se está a executar o seu serviço no máximo crescimento.

É bom ter algumas máquinas extra disponíveis a qualquer momento para que possa lidar com quaisquer picos ou falhas inesperadas (por exemplo, se alguns VMs descerem).  Embora a capacidade extra deva ser determinada utilizando os seus picos esperados, um ponto de partida é reservar alguns VMs extra (5-10 por cento extra).

O anterior assume um único serviço estatal. Se tiver mais do que um serviço estatal, tem de adicionar os DB_Size associados aos outros serviços à equação. Em alternativa, pode calcular o número de nós separadamente para cada serviço imponente.  O seu serviço pode ter réplicas ou divisórias que não sejam equilibradas. Tenha em mente que as divisórias também podem ter mais dados do que outros. Para obter mais informações sobre a partição, consulte [artigo de partição sobre as melhores práticas.](service-fabric-concepts-partitioning.md) No entanto, a equação anterior é partição e réplica agnóstica, porque o Service Fabric garante que as réplicas estão espalhadas entre os nós de forma otimizada.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utilize uma folha de cálculo para o cálculo de custos
Agora vamos colocar alguns números reais na fórmula. Uma [folha de cálculo](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) de exemplo mostra como planear a capacidade de uma aplicação que contém três tipos de objetos de dados. Para cada objeto, aproximamo-nos do seu tamanho e de quantos objetos esperamos ter. Também selecionamos quantas réplicas queremos de cada tipo de objeto. A folha de cálculo calcula a quantidade total de memória a armazenar no cluster.

Depois introduzimos um tamanho VM e um custo mensal. Com base no tamanho do VM, a folha de cálculo indica-lhe o número mínimo de divisórias que deve utilizar para dividir os seus dados para encaixar fisicamente nos nós. Pode desejar um maior número de divisórias para acomodar as necessidades específicas de cálculo e tráfego de rede da sua aplicação. A folha de cálculo mostra que o número de divisórias que estão a gerir os objetos do perfil do utilizador aumentou de um para seis.

Agora, com base em toda esta informação, a folha de cálculo mostra que você poderia fisicamente obter todos os dados com as divisórias e réplicas desejadas num cluster de 26 nós. No entanto, este cluster seria densamente embalado, por isso você pode querer alguns nós adicionais para acomodar falhas e atualizações de nós. A folha de cálculo também mostra que ter mais de 57 nós não fornece nenhum valor adicional porque você teria nós vazios. Mais uma vez, você pode querer ir acima de 57 nós de qualquer maneira para acomodar falhas de nó e atualizações. Pode ajustar a folha de cálculo para corresponder às necessidades específicas da sua aplicação.   

![Folha de cálculo para cálculo de custos][Image1]

## <a name="next-steps"></a>Passos seguintes
Consulte os [serviços de tecido de serviço de partição][10] para saber mais sobre a partilha do seu serviço.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
