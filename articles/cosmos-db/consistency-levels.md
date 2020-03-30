---
title: Níveis de consistência em Azure Cosmos DB
description: A Azure Cosmos DB tem cinco níveis de consistência para ajudar a equilibrar eventuais consistência, disponibilidade e compensações de latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4e3d29471064616039bf946bb2762c15ce67bf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530262"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de consistência em Azure Cosmos DB

As bases de dados distribuídas que dependem da replicação para alta disponibilidade, baixa latência, ou ambas, fazem a troca fundamental entre a consistência da leitura vs. disponibilidade, latência e saída. A maioria das bases de dados distribuídas comercialmente disponíveis pedem aos desenvolvedores que escolham entre os dois modelos de consistência extrema: *forte* consistência e *eventual* consistência. A linearizabilidade ou o modelo de consistência forte é o padrão dourado de programabilidade de dados. Mas adiciona um preço de maior latência (em estado estável) e uma disponibilidade reduzida (durante as falhas). Por outro lado, a eventual consistência oferece maior disponibilidade e melhor desempenho, mas dificulta o programa de aplicações. 

Azure Cosmos DB aborda a consistência dos dados como um espectro de escolhas em vez de dois extremos. A forte consistência e a eventual consistência estão nas extremidades do espectro, mas há muitas escolhas de consistência ao longo do espectro. Os desenvolvedores podem usar estas opções para fazer escolhas precisas e trocas granulares no que diz respeito à elevada disponibilidade e desempenho. 

Com o Azure Cosmos DB, os desenvolvedores podem escolher entre cinco modelos de consistência bem definidos no espectro de consistência. De mais forte a mais descontraído, os modelos incluem *estagnação forte,* *limitada,* *sessão,* *prefixo consistente*e *eventual* consistência. Os modelos são bem definidos e intuitivos e podem ser usados para cenários específicos do mundo real. Cada modelo proporciona trocas de [disponibilidade e desempenho](consistency-levels-tradeoffs.md) e é apoiado pelos SLAs. A imagem que se segue mostra os diferentes níveis de consistência como espectro.

![Consistência como espectro](./media/consistency-levels/five-consistency-levels.png)

Os níveis de consistência são agnósticos da região e são garantidos para todas as operações, independentemente da região a partir da qual são servidas as leituras e os escritos, o número de regiões associadas à sua conta Azure Cosmos, ou se a sua conta está configurada com uma única ou várias regiões de escrita.

## <a name="scope-of-the-read-consistency"></a>Âmbito da consistência da leitura

A consistência da leitura aplica-se a uma única operação de leitura, resumida dentro de uma gama de divisórias ou de uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou por um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

Pode configurar o nível de consistência padrão na sua conta Azure Cosmos a qualquer momento. O nível de consistência predefinido configurado na sua conta aplica-se a todas as bases de dados e contentores da Azure Cosmos nessa conta. Todas as leituras e consultas emitidas contra um recipiente ou uma base de dados utilizam o nível de consistência especificado por defeito. Para saber mais, veja como [configurar o nível](how-to-manage-consistency.md#configure-the-default-consistency-level)de consistência padrão .

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas aos níveis de consistência

Os SLAs abrangentes fornecidos pela Azure Cosmos DB garantem que 100% dos pedidos de leitura cumprem a garantia de consistência para qualquer nível de consistência que escolha. Um pedido de leitura satisfaz a consistência SLA se todas as garantias de coerência associadas ao nível de consistência estiverem satisfeitas. As definições precisas dos cinco níveis de consistência em Azure Cosmos DB utilizando a linguagem de especificação TLA+ são fornecidas no repo [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub.

A semântica dos cinco níveis de consistência é descrita aqui:

- **Forte**: Forte consistência oferece uma garantia de linearizabilidade. A linearizabilidade refere-se ao serviço de pedidos simultaneamente. As leituras são garantidas para devolver a versão mais recente comprometida de um item. Um cliente nunca vê uma escrita não comprometida ou parcial. Os utilizadores têm sempre a garantia de ler as últimas escritas comprometidas.

  O gráfico seguinte ilustra a forte consistência com notas musicais. Depois de os dados forem escritos para a região "West US 2", quando se lê os dados de outras regiões, obtém-se o valor mais recente:

  ![vídeo](media/consistency-levels/strong-consistency.gif)

- **Estagnação limitada**: As leituras são garantidas para honrar a garantia de prefixo consistente. As leituras podem ficar para trás escritas por, na maioria das versões *"K"* (isto é, "atualizações") de um item ou por intervalo de tempo *"T".* Por outras palavras, quando se escolhe a estagnação limitada, a "estagnação" pode ser configurada de duas formas: 

  * O número de versões *(K)* do item
  * O intervalo de tempo *(T)* pelo qual as leituras podem ficar atrás das escritas 

  A estagnação limitada oferece uma ordem global total, exceto dentro da "janela de estagnação". As garantias de leitura monotónica existem dentro de uma região dentro e fora da janela de estagnação. A forte consistência tem a mesma semântica que a oferecida pela estagnação limitada. A janela de estagnação é igual a zero. A estagnação limitada também é referida como uma linhagem retardada pelo tempo. Quando um cliente realiza operações de leitura dentro de uma região que aceita escreve, as garantias fornecidas pela consistência deslimitada são idênticas às garantias pela forte consistência.

  A estagnação limitada é frequentemente escolhida por aplicações distribuídas globalmente que esperam baixas latências de escrita, mas exigem uma garantia total de ordem global. A estagnação limitada é ótima para aplicações com colaboração e partilha de grupos, carraças de ações, publicação/fila, etc. O gráfico seguinte ilustra a consistência deslimitada com notas musicais. Após a escrita dos dados para a região "West US 2", as regiões "East US 2" e "Australia East" lêem o valor escrito com base no tempo máximo de atraso configurado ou nas operações máximas:

  ![vídeo](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sessão**: Dentro de uma única sessão de cliente, as leituras são garantidas para honrar o prefixo consistente (assumindo uma única sessão de "escritor", leituras monotónicas, escritas monotónicas, leitura-suas-escritas e garantias de leitura de escritos. Os clientes fora da sessão a realizar em escrita saem eventuais consistências.

  A consistência da sessão é o nível de consistência amplamente utilizado tanto para a região única como para aplicações distribuídas globalmente. Fornece tardios de escrita, disponibilidade e leitura de entrada comparável à de eventual consistência, mas também fornece as garantias de consistência que se adequam às necessidades das aplicações escritas para operar no contexto de um utilizador. O gráfico seguinte ilustra a consistência da sessão com notas musicais. A região "West US 2" e as regiões "East US 2" estão a usar a mesma sessão (Sessão A) para que ambos leiam os dados ao mesmo tempo. Enquanto a região "Austrália Leste" está a usar a "Sessão B", por isso, recebe dados mais tarde, mas na mesma ordem que as escritas.

  ![vídeo](media/consistency-levels/session-consistency.gif)

- **Prefixo consistente**: As atualizações que são devolvidas contêm algum prefixo de todas as atualizações, sem lacunas. O nível de consistência de prefixo consistente garante que a leitura nunca vê escritos fora de ordem.

  Se as escritas fossem efetuadas na ordem `A, B, C`, então um cliente veria `A`, `A,B` ou `A,B,C`, mas nunca fora de ordem como `A,C` ou `B,A,C`. O Prefixo Consistente fornece lálénitas, disponibilidade e leitura de entrada comparáveis à de eventual consistência, mas também fornece garantias de encomenda que se adequam às necessidades dos cenários em que a ordem é importante. O gráfico seguinte ilustra a consistência prefixação de consistência com notas musicais. Em todas as regiões, as leituras nunca vêem fora de ordem:

  ![vídeo](media/consistency-levels/consistent-prefix.gif)

- **Eventual :** Não há garantia de encomenda para leituras. Na ausência de mais escritos, as réplicas acabam por convergir.  
A eventual consistência é a forma mais fraca de consistência porque um cliente pode ler os valores mais antigos do que os que tinha lido antes. A eventual consistência é ideal quando a aplicação não necessita de garantias de encomenda. Exemplos incluem contagem de Retweets, Likes ou comentários não roscados. O gráfico seguinte ilustra a eventual consistência com notas musicais.

  ![vídeo](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre conceitos de consistência, leia os seguintes artigos:

- [Especificações TLA+ de alto nível para os cinco níveis de consistência oferecidos pela Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistência de dados replicada explicada através do basebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistência de dados replicada explicada através do basebol (whitepaper) por Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Garantias de sessão para dados replicados fracamente consistentes](https://dl.acm.org/citation.cfm?id=383631)
- [Tradeoffs de consistência no design moderno de sistemas de base de dados distribuídos: A CAP é apenas parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Estagnação probabilística limitada (PBS) para Quórums Parciais Práticos](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventualmente Consistente - Revisitado](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os níveis de consistência no Azure Cosmos DB, leia os seguintes artigos:

* [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
* [Níveis de consistência em IA DB](consistency-levels-across-apis.md)
* [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Configurar o nível de consistência predefinido](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Substituir o nível de consistência predefinido](how-to-manage-consistency.md#override-the-default-consistency-level)

