---
title: Planeamento da capacidade para aplicações do Service Fabric | Documentos da Microsoft
description: Descreve como identificar o número de nós de computação necessário para uma aplicação do Service Fabric
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: ''
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: d7ca566b86ed79aa773d7af2553223c79ed9944a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701848"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planeamento da capacidade para aplicações do Service Fabric
Este documento mostra como estimar a quantidade de recursos (CPUs, RAM, armazenamento de disco), que terá de executar as suas aplicações do Azure Service Fabric. É comum para os seus requisitos de recursos alterar ao longo do tempo. Alguns recursos exigem, normalmente, à medida que seu serviço de desenvolvimento/teste e, em seguida, requerem mais recursos à medida que entra em produção e a sua aplicação cresce em popularidade. Quando criar seu aplicativo, considerar os requisitos de longo prazo e fazer escolhas que permitem ao serviço dimensionam para satisfazer a procura dos clientes elevada.

 Quando cria um cluster do Service Fabric, decidir o que tipos de máquinas virtuais (VMs) compõem o cluster. Cada VM é fornecido com uma quantidade limitada de recursos sob a forma de CPUs (núcleos e velocidade), largura de banda de rede, RAM e armazenamento em disco. À medida que aumenta o serviço ao longo do tempo, pode atualizar para VMs que oferecem muitos recursos e/ou adicionar mais VMs ao seu cluster. Para fazer a última opção, deve projetar seu serviço inicialmente para que possa tirar partido de novas VMs que são adicionados dinamicamente ao cluster.

Alguns serviços não gerir pouco ou nenhum dados nas VMs propriamente ditas. Por conseguinte, o planeamento da capacidade para estes serviços deve se concentrar principalmente no desempenho, o que significa que selecionar as CPUs apropriadas (núcleos e velocidade) das VMs. Além disso, deve considerar a largura de banda de rede, incluindo a frequência de ocorrência de transferências de rede e a quantidade de dados está a ser transferido. Se o serviço precisar executar bem à medida que aumenta de utilização do serviço, pode adicionar mais VMs para o saldo de cluster e a carga da rede de pedidos por todas as VMs.

Para os serviços que gerem grandes quantidades de dados nas VMs, planeamento de capacidade deve se concentrar principalmente em tamanho. Assim, deve considerar cuidadosamente a capacidade de RAM da VM e o armazenamento de disco. O sistema de gerenciamento de memória virtual no Windows faz o seguinte aspeto RAM ao código da aplicação de espaço em disco. Além disso, o tempo de execução do Service Fabric fornece a paginação inteligente manter apenas dados na memória e mover os dados amovíveis para disco. Aplicativos, portanto, podem usar mais memória do que é fisicamente disponível na VM. Simplesmente ter mais RAM aumenta o desempenho, uma vez que a VM pode ter mais armazenamento de disco em RAM. A VM que selecionar deve ter um grande o suficiente para armazenar os dados que pretende na VM de disco. Da mesma forma, a VM deve ter RAM suficiente para lhe fornecer o desempenho desejado. Se os dados do seu serviço que aumenta ao longo do tempo, pode adicionar mais VMs para o cluster e a partição dos dados em todas as VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determinar a quantidade de nós é necessário
O serviço de criação de partições permite-lhe aumentar horizontalmente os dados do seu serviço. Para obter mais informações sobre a criação de partições, consulte [criação de partições de Service Fabric](service-fabric-concepts-partitioning.md). Cada partição deve caber dentro de uma única VM, mas várias partições de (pequenas) podem ser colocadas numa única VM. Então, ter mais de partições pequenas dá-lhe mais flexibilidade do que algumas partições maiores. A desvantagem é que ter muitos partições aumenta a sobrecarga de Service Fabric e não é possível executar operações transacionadas entre partições. Também há mais tráfego de rede potencial se seu código de serviço com freqüência precisa acessar partes de dados que residem em partições diferentes. Ao projetar o seu serviço, deve considerar cuidadosamente esses prós e contras deparar-se com uma estratégia de particionamento eficaz.

Vamos supor que seu aplicativo tiver um único serviço com estado que tenha um tamanho de armazenamento que pretende aumentar a DB_Size GB num ano. Está disposto a adicionar mais aplicações (e partições) como experiência o crescimento além esse ano.  O fator de replicação (RF), que determina o número de réplicas para o seu serviço tem impacto sobre o DB_Size total. O DB_Size total em todas as réplicas é o fator de replicação multiplicado por DB_Size.  Node_Size representa a RAM/espaço em disco por nó que pretende utilizar para o seu serviço. Para obter melhor desempenho, a DB_Size deve se encaixam na memória no cluster e um Node_Size que está em torno da RAM da VM deve ser escolhido. Ao alocar uma Node_Size que é maior do que a capacidade de RAM, está a depender a paginação fornecida pelo runtime do Service Fabric. Portanto, o desempenho pode não ser ideal se seus dados de inteiros são considerados como acesso frequente (desde então, os dados é do bloco paginados de entrada/saída). No entanto, para muitos serviços em que é frequente apenas uma fração dos dados, é mais económico.

O número de nós necessários para um desempenho máximo pode ser calculado da seguinte forma:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Conta o crescimento
Talvez queira computar o número de nós com base no DB_Size que espera que o seu serviço a crescer, além do DB_Size que começou com. Em seguida, aumente o número de nós à medida que cresce seu serviço para que não estão no superprovisionamento o número de nós. Mas o número de partições deve basear-se no número de nós que são necessários quando estiver a executar o seu serviço em crescimento máximo.

É bom ter algumas máquinas Extras disponíveis em qualquer altura, para que pode manipular qualquer picos inesperados ou falha (por exemplo, se algumas VMs forem abaixo).  Embora a capacidade extra deve ser determinada através de seu picos esperados, um ponto de partida é reservar alguns VMs adicionais (5 a 10% extra).

Anterior assume um único serviço com estado. Se tiver mais do que um serviço com estado, terá de adicionar o DB_Size associados com os outros serviços na equação. Em alternativa, pode computar o número de nós em separado para cada serviço com estado.  Seu serviço pode ter réplicas ou partições que não são balanceadas. Tenha em atenção que as partições também podem ter mais dados do que outras pessoas. Para obter mais informações sobre a criação de partições, consulte [artigo sobre melhores práticas de criação de partições](service-fabric-concepts-partitioning.md). No entanto, a equação anterior é a partição e réplica agnóstico, porque o Service Fabric garante que as réplicas são distribuídas entre os nós de forma otimizada.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utilizar uma folha de cálculo para o cálculo do custo
Agora vamos colocar alguns números reais na fórmula. Uma [folha de cálculo de exemplo](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) mostra como planear a capacidade para uma aplicação que contém três tipos de objetos de dados. Para cada objeto, podemos fazer uma aproximação seu tamanho e quantos objetos que espera ter. Selecionamos também como muitas réplicas que queremos de cada tipo de objeto. A folha de cálculo calcula a quantidade total de memória a ser armazenados no cluster.

Em seguida, introduza um tamanho VM e o custo mensal. Com base no tamanho VM, a folha de cálculo indica o número mínimo de partições que tem de utilizar para dividir os dados de acordo com fisicamente em nós. Pode desejar um grande número de partições para acomodar o cálculo específico do seu aplicativo e necessidades de tráfego de rede. A folha de cálculo mostra o número de partições que estiver a gerir os objetos de perfil de utilizador aumentou a partir de um para seis.

Agora, com base em todas essas informações, a folha de cálculo mostra que fisicamente foi possível obter todos os dados com o desejado de partições e réplicas num cluster de nó de 26. No entanto, este cluster seria ser densamente, pelo que deve alguns nós adicionais para acomodar as atualizações e falhas de nó. A folha de cálculo também mostra que ter mais de 57 nós fornece nenhum valor adicional porque teria que nós vazios. Novamente, talvez deseje migrar acima 57 nós de qualquer forma, para acomodar as atualizações e falhas de nó. É possível ajustar a folha de cálculo para corresponder a necessidades específicas da sua aplicação.   

![Folha de cálculo para o cálculo do custo][Image1]

## <a name="next-steps"></a>Passos Seguintes
Confira [serviços de criação de partições de Service Fabric] [ 10] para saber mais sobre o seu serviço de criação de partições.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
