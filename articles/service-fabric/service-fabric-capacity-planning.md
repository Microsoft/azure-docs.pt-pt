---
title: Planeamento de capacidade para aplicações de tecido de serviço
description: Descreve como identificar o número de nós de computação necessários para uma aplicação de Tecido de Serviço
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377213"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planeamento de capacidade seleções para aplicações de tecido de serviço
Este documento ensina-lhe como estimar a quantidade de recursos (CPUs, RAM, armazenamento de discos) que precisa para executar as suas aplicações Azure Service Fabric. É comum que os seus requisitos de recursos mudem ao longo do tempo. Normalmente, você precisa de poucos recursos à medida que desenvolve/testa o seu serviço, e depois requer mais recursos à medida que entra em produção e a sua aplicação cresce em popularidade. Quando conceber a sua aplicação, pense nos requisitos a longo prazo e faça escolhas que permitam ao seu serviço escalar para satisfazer a elevada procura do cliente.

 Quando cria um cluster de Tecido de Serviço, decide que tipos de máquinas virtuais (VMs) compõem o cluster. Cada VM vem com uma quantidade limitada de recursos sob a forma de CPUs (núcleos e velocidade), largura de banda da rede, RAM e armazenamento de discos. À medida que o seu serviço cresce ao longo do tempo, pode fazer upgrade para VMs que oferecem maiores recursos e/ou adicionar mais VMs ao seu cluster. Para fazer este último, você deve arquiteto o seu serviço inicialmente para que possa tirar partido de novos VMs que são adicionados dinamicamente ao cluster.

Alguns serviços gerem pouco ou nenhum dado sobre os próprios VMs. Por conseguinte, o planeamento da capacidade destes serviços deve centrar-se principalmente no desempenho, o que significa selecionar os CPUs apropriados (núcleos e velocidade) dos VMs. Além disso, deve considerar a largura de banda da rede, incluindo a frequência com que as transferências de rede estão a ocorrer e quanto suferiu dados. Se o seu serviço precisar de funcionar bem assim como o uso do serviço aumenta, pode adicionar mais VMs ao cluster e carregar o equilíbrio da rede em todos os VMs.

Para serviços que gerem grandes quantidades de dados sobre os VMs, o planeamento da capacidade deve centrar-se principalmente na dimensão. Assim, deve considerar cuidadosamente a capacidade do ARMAZENAMENTO DE RAM e disco da VM. O sistema de gestão de memória virtual no Windows faz com que o espaço do disco pareça RAM para o código de aplicação. Além disso, o tempo de execução do Tecido de Serviço fornece uma pagagem inteligente mantendo apenas dados quentes na memória e movendo os dados frios para o disco. As aplicações podem, assim, utilizar mais memória do que fisicamente disponível no VM. Ter mais RAM simplesmente aumenta o desempenho, uma vez que o VM pode manter mais armazenamento de disco em RAM. O VM que selecionar deve ter um disco suficientemente grande para armazenar os dados que pretende no VM. Da mesma forma, o VM deve ter RAM suficiente para lhe proporcionar o desempenho que deseja. Se os dados do seu serviço crescerem ao longo do tempo, pode adicionar mais VMs ao cluster e dividir os dados em todos os VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determine quantos nódosos precisa
A partilha do seu serviço permite-lhe reduzir os dados do seu serviço. Para obter mais informações sobre a partilha, consulte O [Tecido de Serviço de Partição](service-fabric-concepts-partitioning.md). Cada divisória deve caber num único VM, mas várias divisórias (pequenas) podem ser colocadas num único VM. Então, ter mais divisórias pequenas dá-lhe maior flexibilidade do que ter algumas divisórias maiores. A compensação é que ter muitas divisórias aumenta a sobrecarga do Tecido de Serviço e não pode realizar operações transacionadas através de divisórias. Existe também um tráfego de rede mais potencial se o seu código de serviço precisar frequentemente de aceder a peças de dados que vivem em divisórias diferentes. Ao conceber o seu serviço, deve considerar cuidadosamente estes prós e contras para chegar a uma estratégia eficaz de partição.

Vamos assumir que a sua aplicação tem um único serviço estatal que tem um tamanho de loja que espera crescer para DB_Size GB em um ano. Está disposto a adicionar mais aplicações (e divisórias) à medida que experimenta um crescimento para além desse ano.  O fator de replicação (RF), que determina o número de réplicas para o seu serviço, afeta o total DB_Size. O total DB_Size em todas as réplicas é o Fator de Replicação multiplicado por DB_Size.  Node_Size representa o espaço do disco/RAM por nó que pretende utilizar para o seu serviço. Para um melhor desempenho, o DB_Size deve caber na memória através do cluster, e um Node_Size que esteja em torno da RAM do VM deve ser escolhido. Ao atribuir um Node_Size maior do que a capacidade de RAM, está a contar com a pagagem fornecida pelo tempo de execução do Tecido de Serviço. Assim, o seu desempenho pode não ser o ideal se todos os seus dados forem considerados quentes (desde então os dados são páginados dentro/fora). No entanto, para muitos serviços onde apenas uma fração dos dados é quente, é mais rentável.

O número de nós necessários para o máximo desempenho pode ser calculado da seguinte forma:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Conta para o crescimento
Pode querer calcular o número de nós com base no DB_Size que espera que o seu serviço cresça, além da DB_Size com que começou. Depois, aumente o número de nós à medida que o seu serviço cresce para que não esteja a fornecer demasiado o número de nós. Mas o número de divisórias deve basear-se no número de nós que são necessários quando se está a executar o seu serviço com o máximo de crescimento.

É bom ter algumas máquinas extra disponíveis a qualquer momento para que possa lidar com quaisquer picos ou falhas inesperados (por exemplo, se alguns VMs descerem).  Embora a capacidade extra deva ser determinada utilizando os seus picos esperados, um ponto de partida é reservar alguns VMs extra (5-10 por cento extra).

O anterior assume um único serviço estatal. Se tiver mais de um serviço de estado, tem de adicionar o DB_Size associado aos outros serviços na equação. Em alternativa, pode calcular o número de nós separadamente para cada serviço de estado.  O seu serviço pode ter réplicas ou divisórias que não sejam equilibradas. Tenha em mente que as divisórias também podem ter mais dados do que outros. Para obter mais informações sobre a partilha, consulte o [artigo de partilha sobre as melhores práticas.](service-fabric-concepts-partitioning.md) No entanto, a equação anterior é a partição e a réplica agnóstica, porque o Tecido de Serviço garante que as réplicas estão espalhadas entre os nós de forma otimizada.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utilize uma folha de cálculo para cálculo de custos
Agora vamos colocar alguns números reais na fórmula. Uma folha de [cálculo de exemplo](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) mostra como planear a capacidade de uma aplicação que contém três tipos de objetos de dados. Para cada objeto, aproximamo-nos do seu tamanho e de quantos objetos esperamos ter. Também selecionamos quantas réplicas queremos de cada tipo de objeto. A folha de cálculo calcula a quantidade total de memória a armazenar no cluster.

Depois entramos num tamanho VM e custo mensal. Com base no tamanho do VM, a folha de cálculo indica-lhe o número mínimo de divisórias que deve utilizar para dividir os seus dados para se encaixar fisicamente nos nós. Pode desejar um maior número de divisórias para acomodar as necessidades específicas de computação e tráfego de rede da sua aplicação. A folha de cálculo mostra que o número de divisórias que estão a gerir os objetos de perfil do utilizador aumentou de um para seis.

Agora, com base em toda esta informação, a folha de cálculo mostra que você poderia obter fisicamente todos os dados com as divisórias e réplicas desejadas em um cluster de 26 nós. No entanto, este cluster seria densamente embalado, por isso você pode querer alguns nós adicionais para acomodar falhas e upgrades de nós. A folha de cálculo também mostra que ter mais de 57 nós não fornece valor adicional porque você teria nós vazios. Mais uma vez, você pode querer ir acima de 57 nós de qualquer maneira para acomodar falhas de nós e upgrades. Pode ajustar a folha de cálculo para corresponder às necessidades específicas da sua aplicação.   

![Folha de cálculo para cálculo de custos][Image1]

## <a name="next-steps"></a>Passos seguintes
Consulte os serviços de Tecido de Serviço de [Partição][10] para saber mais sobre a partilha do seu serviço.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
