---
title: Planejamento de capacidade para aplicativos Service Fabric
description: Descreve como identificar o número de nós de computação necessários para um aplicativo Service Fabric
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75377213"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planejamento de capacidade para aplicativos Service Fabric
Este documento ensina como estimar a quantidade de recursos (CPUs, RAM, armazenamento em disco) de que você precisa para executar seus aplicativos de Service Fabric do Azure. É comum que os requisitos de recursos sejam alterados ao longo do tempo. Normalmente, você precisa de poucos recursos à medida que desenvolve/testa seu serviço e, em seguida, exige mais recursos à medida que passa para a produção e seu aplicativo cresce em popularidade. Ao projetar seu aplicativo, considere os requisitos de longo prazo e faça escolhas que permitam a escala do seu serviço para atender à alta demanda do cliente.

 Ao criar um Cluster Service Fabric, você decide quais tipos de máquinas virtuais (VMs) compõem o cluster. Cada VM vem com uma quantidade limitada de recursos na forma de CPUs (núcleos e velocidade), largura de banda de rede, RAM e armazenamento em disco. À medida que seu serviço cresce ao longo do tempo, você pode atualizar para VMs que oferecem mais recursos e/ou adicionar mais VMs ao cluster. Para fazer o último, você deve arquitetar seu serviço inicialmente para que ele possa aproveitar as novas VMs que são adicionadas dinamicamente ao cluster.

Alguns serviços gerenciam pouco ou nenhum dado nas VMs em si. Portanto, o planejamento de capacidade para esses serviços deve se concentrar principalmente no desempenho, o que significa selecionar as CPUs apropriadas (núcleos e velocidade) das VMs. Além disso, você deve considerar a largura de banda da rede, incluindo a frequência com que as transferências de rede estão ocorrendo e a quantidade de dados sendo transferida. Se o serviço precisar ser executado bem como o uso do serviço aumentar, você poderá adicionar mais VMs ao cluster e balancear a carga das solicitações de rede em todas as VMs.

Para serviços que gerenciam grandes quantidades de dados nas VMs, o planejamento de capacidade deve se concentrar principalmente no tamanho. Portanto, você deve considerar cuidadosamente a capacidade da RAM e do armazenamento em disco da VM. O sistema de gerenciamento de memória virtual no Windows torna o espaço em disco parecido com a RAM para o código do aplicativo. Além disso, o tempo de execução de Service Fabric fornece paginação inteligente mantendo apenas os dados ativos na memória e movendo os dados frios para o disco. Assim, os aplicativos podem usar mais memória do que está fisicamente disponível na VM. Ter mais RAM simplesmente aumenta o desempenho, já que a VM pode manter mais armazenamento em disco na RAM. A VM selecionada deve ter um disco grande o suficiente para armazenar os dados que você deseja na VM. Da mesma forma, a VM deve ter RAM suficiente para fornecer o desempenho que você deseja. Se os dados do serviço aumentarem ao longo do tempo, você poderá adicionar mais VMs ao cluster e particionar os dados em todas as VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determinar quantos nós você precisa
O particionamento do serviço permite que você Escale horizontalmente os dados do serviço. Para obter mais informações sobre particionamento, consulte [particionamento de Service Fabric](service-fabric-concepts-partitioning.md). Cada partição deve caber em uma única VM, mas várias partições (pequenas) podem ser colocadas em uma única VM. Portanto, ter mais partições pequenas proporciona maior flexibilidade do que ter algumas partições maiores. A desvantagem é que ter muitas partições aumenta Service Fabric sobrecarga e você não pode executar operações transacionadas entre partições. Também há mais tráfego de rede potencial se o código do serviço frequentemente precisar acessar partes de dados que residem em partições diferentes. Ao projetar seu serviço, você deve considerar cuidadosamente esses prós e contras para chegar a uma estratégia de particionamento eficaz.

Vamos supor que seu aplicativo tem um único serviço com estado que tem um tamanho de armazenamento que você espera aumentar para DB_Size GB em um ano. Você está disposto a adicionar mais aplicativos (e partições) à medida que enfrenta o crescimento além daquele ano.  O fator de replicação (RF), que determina o número de réplicas para seu serviço, afeta o total de DB_Size. O total de DB_Size em todas as réplicas é o fator de replicação multiplicado por DB_Size.  Node_Size representa o espaço em disco/RAM por nó que você deseja usar para o serviço. Para obter o melhor desempenho, o DB_Size deve se ajustar à memória no cluster e uma Node_Size que esteja em redor da RAM da VM deve ser escolhida. Ao alocar um Node_Size maior que a capacidade de RAM, você depende da paginação fornecida pelo Service Fabric Runtime. Portanto, seu desempenho pode não ser ideal se todos os seus dados forem considerados ativos (desde então, os dados serão paginados/desativados). No entanto, para muitos serviços em que apenas uma fração dos dados está quente, ela é mais econômica.

O número de nós necessários para o desempenho máximo pode ser calculado da seguinte maneira:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Conta para crescimento
Você pode desejar calcular o número de nós com base no DB_Size que você espera que seu serviço cresça, além do DB_Size que você começou com. Em seguida, aumente o número de nós à medida que seu serviço cresce para que você não esteja Provisionando o número de nós. Mas o número de partições deve ser baseado no número de nós que são necessários quando você está executando seu serviço no crescimento máximo.

É bom ter algumas máquinas extras disponíveis a qualquer momento para que você possa lidar com picos ou falhas inesperados (por exemplo, se algumas VMs ficarem inativas).  Embora a capacidade extra deva ser determinada usando os picos esperados, um ponto de partida é reservar algumas VMs extras (5-10% extra).

O anterior pressupõe um único serviço com estado. Se você tiver mais de um serviço com estado, precisará adicionar os DB_Size associados aos outros serviços na equação. Como alternativa, você pode calcular o número de nós separadamente para cada serviço com estado.  Seu serviço pode ter réplicas ou partições que não são balanceadas. Tenha em mente que as partições também podem ter mais dados do que outras. Para obter mais informações sobre particionamento, consulte o [artigo sobre particionamento em práticas recomendadas](service-fabric-concepts-partitioning.md). No entanto, a equação anterior é independente de partição e réplica, porque Service Fabric garante que as réplicas sejam distribuídas entre os nós de maneira otimizada.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Usar uma planilha para o cálculo de custo
Agora, vamos colocar alguns números reais na fórmula. Uma [planilha de exemplo](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) mostra como planejar a capacidade de um aplicativo que contém três tipos de objetos de dados. Para cada objeto, aproximamos seu tamanho e quantos objetos esperamos ter. Também selecionamos quantas réplicas desejamos de cada tipo de objeto. A planilha calcula a quantidade total de memória a ser armazenada no cluster.

Em seguida, inserimos um tamanho de VM e um custo mensal. Com base no tamanho da VM, a planilha informa o número mínimo de partições que você deve usar para dividir os dados para se ajustarem fisicamente aos nós. Você pode desejar um número maior de partições para acomodar as necessidades específicas de tráfego de rede e computação de seu aplicativo. A planilha mostra o número de partições que estão gerenciando os objetos de perfil do usuário aumentou de um para seis.

Agora, com base em todas essas informações, a planilha mostra que você pode obter fisicamente todos os dados com as partições e réplicas desejadas em um cluster de 26 nós. No entanto, esse cluster seria empacotado de forma densa, portanto, talvez você queira que alguns nós adicionais acomodem falhas e atualizações de nó. A planilha também mostra que ter mais de 57 nós não fornece nenhum valor adicional porque você teria nós vazios. Novamente, talvez você queira ir acima de 57 nós de qualquer forma para acomodar falhas e atualizações de nó. Você pode ajustar a planilha para corresponder às necessidades específicas do seu aplicativo.   

![Planilha para cálculo de custo][Image1]

## <a name="next-steps"></a>Passos seguintes
Confira [particionamento Service Fabric serviços][10] para saber mais sobre como particionar seu serviço.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
