---
title: Níveis de consistência no Azure Cosmos DB
description: A Azure Cosmos DB tem cinco níveis de consistência para ajudar a equilibrar eventuais trocas de consistência, disponibilidade e latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 27c1a896d25a0db00ff5f263d949f6657a658e3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567207"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>O que são níveis de consistência no Azure Cosmos DB?

Bases de dados distribuídas que dependem da replicação para alta disponibilidade, baixa latência, ou ambos, fazem a troca fundamental entre a consistência de leitura vs. disponibilidade, latência e produção. A maioria das bases de dados distribuídas comercialmente pedem aos desenvolvedores que escolham entre os dois modelos de consistência extrema: *forte* consistência e *eventual* consistência. A linearizabilidade do modelo de consistência forte é o padrão dourado da programabilidade de dados. Mas adiciona um preço de maior latência de escrita (em estado estável) e disponibilidade reduzida (durante as falhas). Por outro lado, uma eventual consistência oferece maior disponibilidade e melhor desempenho, mas dificulta o programa de aplicações.

Azure Cosmos DB aborda a consistência dos dados como um espectro de escolhas em vez de dois extremos. Os desenvolvedores podem usar estas opções para fazer escolhas precisas e trocas granulares no que diz respeito à alta disponibilidade e desempenho.

Com a Azure Cosmos DB, os desenvolvedores podem escolher entre cinco níveis de consistência bem definidos no espectro de consistência. Estes níveis incluem *forte,* *limitado,* *sessão,* *prefixo consistente*e *eventual* consistência. Os níveis são bem definidos e intuitivos e podem ser usados para cenários específicos do mundo real. Cada nível fornece [compensações de disponibilidade e desempenho](consistency-levels-tradeoffs.md) e são apoiados por SLAs. A imagem a seguir mostra os diferentes níveis de consistência como espectro.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Consistência como espectro" border="false" :::

Os níveis de consistência são agnósticos regiões e são garantidos para todas as operações, independentemente da região a partir da qual são servidas as leituras e as escritas, o número de regiões associadas à sua conta Azure Cosmos, ou se a sua conta está configurada com uma única ou múltiplas regiões de escrita.

## <a name="scope-of-the-read-consistency"></a>Âmbito da consistência da leitura

A consistência da leitura aplica-se a uma única operação de leitura enquadrada numa partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou por um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

Pode configurar o nível de consistência padrão na sua conta Azure Cosmos a qualquer momento. O nível de consistência padrão configurado na sua conta aplica-se a todas as bases de dados e contentores da Azure Cosmos nessa conta. Todas as leituras e consultas emitidas contra um contentor ou uma base de dados utilizam por defeito o nível de consistência especificado. Para saber mais, consulte como [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level). Também pode anular o nível de consistência predefinido para um pedido específico, para saber mais, ver como anular o artigo [de nível de consistência predefinido.](how-to-manage-consistency.md?#override-the-default-consistency-level)

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas aos níveis de consistência

As SLAs abrangentes fornecidas pela Azure Cosmos DB garantem que 100% dos pedidos de leitura cumprem a garantia de consistência para qualquer nível de consistência que escolha. Um pedido de leitura satisfaz a consistência SLA se todas as garantias de consistência associadas ao nível de consistência estiverem satisfeitas. As definições precisas dos cinco níveis de consistência no Azure Cosmos DB utilizando a linguagem de especificação TLA+ são fornecidas no repo [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub.

A semântica dos cinco níveis de consistência é descrita aqui:

- **Forte**: Forte consistência oferece uma garantia de linearizabilidade. Linearizability refere-se a servir pedidos simultaneamente. As leituras são garantidas para devolver a versão mais recente comprometida de um item. Um cliente nunca vê uma escrita não comprometida ou parcial. Os utilizadores têm sempre a garantia de ler a mais recente escrita comprometida.

  O gráfico que se segue ilustra a forte consistência com notas musicais. Depois de os dados terem sido escritos para a região "West US 2", quando lê os dados de outras regiões, obtém-se o valor mais recente:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Consistência como espectro" pode ser configurada de duas maneiras:

- O número de versões *(K)* do item
- O intervalo de tempo *(T)* pelo qual as leituras podem ficar atrás das escritas

A estagnação limitada oferece uma ordem global total fora da "janela velha". Quando um cliente realiza operações de leitura dentro de uma região que aceita escritas, as garantias dadas pela consistência limitada são idênticas às garantias pela forte consistência.

Dentro da janela de estagnação, a estagnação limitada fornece as seguintes garantias de consistência:

- Consistência para clientes na mesma região para uma conta com região de escrita única = Forte
- Consistência para clientes em diferentes regiões para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes que escrevem para uma única região para uma conta com múltiplas regiões de escrita = Prefix consistente
- Consistência para clientes que escrevem em diferentes regiões para uma conta com múltiplas regiões de escrita = Eventual

  A estagnação limitada é frequentemente escolhida por aplicações distribuídas globalmente que esperam baixas latências de escrita, mas requerem total garantia de ordem global. A estagnação limitada é ótima para aplicações com colaboração e partilha de grupos, ticker de stock, publicação-subscrever/fila, etc. O gráfico a seguir ilustra a consistência deslimícula com notas musicais. Após a escrita dos dados para a região "West US 2", as regiões "Leste DOS EUA 2" e "Austrália Oriental" lêem o valor escrito baseado no tempo de atraso máximo configurado ou nas operações máximas:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Consistência como espectro" ou partilhar o símbolo da sessão para vários escritores.

Os clientes fora da sessão que realizam escritas verão as seguintes garantias:

- Consistência para clientes na mesma região para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes em diferentes regiões para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes que escrevem para uma única região para uma conta com múltiplas regiões de escrita = Prefix consistente
- Consistência para clientes que escrevem em várias regiões para uma conta com múltiplas regiões de escrita = Eventual

  A consistência da sessão é o nível de consistência mais utilizado tanto para uma região como para aplicações distribuídas globalmente. Fornece latências escritas, disponibilidade e produção de leitura comparáveis às de eventual consistência, mas também fornece garantias de consistência que se adequam às necessidades das aplicações escritas para operar no contexto de um utilizador. O gráfico que se segue ilustra a consistência da sessão com notas musicais. O "West US 2 writer" e o "West US 2 reader" estão a usar a mesma sessão (Sessão A) para que ambos leiam os mesmos dados ao mesmo tempo. Enquanto a região "Australia East" está a usar a "Sessão B" por isso, recebe dados mais tarde, mas na mesma ordem que os escritos.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Consistência como espectro":::

- **Prefixo consistente**: As atualizações devolvidas contêm algum prefixo de todas as atualizações, sem lacunas. Garantias consistentes de nível de consistência prefixo que lê nunca ver escritos fora de ordem.

Se as gravações forem realizadas na `A, B, C` ordem, então um cliente vê ou `A` , ou , mas nunca `A,B` `A,B,C` permutações fora de ordem como `A,C` ou `B,A,C` . O Prefixo consistente fornece latências de escrita, disponibilidade e produção de leitura comparáveis às de eventual consistência, mas também fornece garantias de encomenda que se adequam às necessidades dos cenários em que a ordem é importante. 

Abaixo estão as garantias de consistência para Prefix consistente:

- Consistência para clientes na mesma região para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes em diferentes regiões para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes que escrevem para uma única região para uma conta com região de escrita múltipla = Prefix consistente
- Consistência para clientes que escrevem em várias regiões para uma conta com múltiplas regiões de escrita = Eventual

O gráfico a seguir ilustra a consistência do prefixo de consistência com notas musicais. Em todas as regiões, as leituras nunca vêem fora de ordem escreve:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Consistência como espectro":::

- **Eventual:** Não há garantia de encomenda para leituras. Na ausência de escritas adicionais, as réplicas acabam por convergir.  
A consistência eventual é a forma mais fraca de consistência porque um cliente pode ler os valores que são mais antigos do que os que tinha lido antes. A eventual consistência é ideal quando a aplicação não requer quaisquer garantias de encomenda. Exemplos incluem contagem de retweets, gostos ou comentários não roscados. O gráfico a seguir ilustra a eventual consistência com notas musicais.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="Consistência como espectro":::

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre conceitos de consistência, leia os seguintes artigos:

- [Especificações de alto nível TLA+ para os cinco níveis de consistência oferecidos pela Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistência de dados replicada explicada através do basebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistência de dados replicada explicada através do basebol (papel branco) por Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Garantias de sessão para dados replicados fracamente consistentes](https://dl.acm.org/citation.cfm?id=383631)
- [Trocas de consistência em design moderno de sistemas de base de dados distribuídos: CAP é apenas parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) para Quórums Parciais Práticos](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventualmente Consistente - Revisitado](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os níveis de consistência no Azure Cosmos DB, leia os seguintes artigos:

- [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
- [Níveis de consistência em Azure Cosmos DB APIs](consistency-levels-across-apis.md)
- [Disponibilidade e compensações de desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
- [Configurar o nível de consistência predefinido](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Substituir o nível de consistência predefinido](how-to-manage-consistency.md#override-the-default-consistency-level)
