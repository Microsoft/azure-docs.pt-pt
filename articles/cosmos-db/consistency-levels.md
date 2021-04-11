---
title: Níveis de consistência no Azure Cosmos DB
description: A Azure Cosmos DB tem cinco níveis de consistência para ajudar a equilibrar eventuais trocas de consistência, disponibilidade e latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0a203531e026d00b274ac98784076d33b22666d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800147"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de consistência no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

As bases de dados distribuídas que dependem da replicação para a elevada disponibilidade, baixa latência, ou ambas, devem fazer uma troca fundamental entre a consistência da leitura, disponibilidade, latência e produção tal como definida pelo [teorema paclc.](https://en.wikipedia.org/wiki/PACELC_theorem) A linearizabilidade do modelo de consistência forte é o padrão dourado da programabilidade de dados. Mas adiciona um preço elevado de latências de escrita mais altas devido a dados que têm que replicar e comprometer-se em grandes distâncias. Uma forte consistência também pode sofrer de uma disponibilidade reduzida (durante falhas) porque os dados não podem replicar-se e comprometer-se em todas as regiões. A eventual consistência oferece maior disponibilidade e melhor desempenho, mas é mais difícil programar aplicações porque os dados podem não ser completamente consistentes em todas as regiões.

A maioria das bases de dados NoSQL distribuídas comercialmente disponíveis no mercado hoje fornecem apenas consistência forte e eventual. A Azure Cosmos DB oferece cinco níveis bem definidos. Dos mais fortes aos mais fracos, os níveis são:

- *Forte*
- *Estagnação limitada*
- *Sessão*
- *Prefixo consistente*
- *Eventual*

Cada nível proporciona compensações de disponibilidade e desempenho. A imagem a seguir mostra os diferentes níveis de consistência como espectro.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Consistência como espectro" border="false" :::

Os níveis de consistência são agnósticos regiões e são garantidos para todas as operações, independentemente da região a partir da qual são servidas as leituras e as escritas, o número de regiões associadas à sua conta Azure Cosmos, ou se a sua conta está configurada com uma única ou múltiplas regiões de escrita.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

A Azure Cosmos DB fornece suporte nativo para APIs compatíveis com protocolos de arame para bases de dados populares. Estes incluem MongoDB, Apache Cassandra, Gremlin e Azure Table armazenamento. Ao utilizar a API Gremlin e a API de tabela, é utilizado o nível de consistência padrão configurado na conta Azure Cosmos. Para detalhes sobre o mapeamento de nível de consistência entre a API cassandra ou a API para os níveis de consistência da MongoDB e da Azure Cosmos, o mapeamento da consistência da [API](cassandra-consistency.md) de Cassandra e [a API para mapeamento de consistência mongoDB.](mongodb-consistency.md)

## <a name="scope-of-the-read-consistency"></a>Âmbito da consistência da leitura

A consistência da leitura aplica-se a uma única operação de leitura enquadrada numa partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou por um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

Pode configurar o nível de consistência padrão na sua conta Azure Cosmos a qualquer momento. O nível de consistência padrão configurado na sua conta aplica-se a todas as bases de dados e contentores da Azure Cosmos nessa conta. Todas as leituras e consultas emitidas contra um contentor ou uma base de dados utilizam por defeito o nível de consistência especificado. Para saber mais, consulte como [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level). Também pode anular o nível de consistência predefinido para um pedido específico, para saber mais, ver como anular o artigo [de nível de consistência predefinido.](how-to-manage-consistency.md?#override-the-default-consistency-level)

> [!IMPORTANT]
> É necessário recriar qualquer instância SDK depois de alterar o nível de consistência padrão. Isto pode ser feito reiniciando a aplicação. Isto garante que o SDK utiliza o novo nível de consistência predefinido.

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas aos níveis de consistência

A Azure Cosmos DB garante que 100% dos pedidos de leitura cumprem a garantia de consistência para o nível de consistência escolhido. As definições precisas dos cinco níveis de consistência no Azure Cosmos DB utilizando a linguagem de especificação TLA+ são fornecidas no repo [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub.

A semântica dos cinco níveis de consistência é descrita nas seguintes secções.

### <a name="strong-consistency"></a>Consistência forte

A consistência forte fornece uma garantia de transação atómica. Linearizability refere-se a servir pedidos simultaneamente. As leituras são garantidas para devolver a versão mais recente comprometida de um item. Um cliente nunca vê uma escrita não comprometida ou parcial. Os utilizadores têm sempre a garantia de ler a mais recente escrita comprometida.

  O gráfico que se segue ilustra a forte consistência com notas musicais. Depois de os dados terem sido escritos para a região "West US 2", quando lê os dados de outras regiões, obtém-se o valor mais recente:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Ilustração de forte nível de consistência":::

### <a name="bounded-staleness-consistency"></a>Consistência de estagnação limitada

Na consistência limitada, as leituras são garantidas para honrar a garantia consistente de prefixo. As leituras podem ficar atrás de escritos pela maioria das versões *"K"* (isto é, "atualizações") de um item ou por intervalo de tempo *"T",* o que for alcançado primeiro. Por outras palavras, quando se escolhe a estagnação limitada, a "estagnação" pode ser configurada de duas maneiras:

- O número de versões *(K)* do item
- O intervalo de tempo *(T)* pode ficar atrás das escritas

Para uma única conta de região, o valor mínimo de *K* e *T* é de 10 operações de escrita ou 5 segundos. Para contas multi-regiões, o valor mínimo de *K* e *T* é de 100.000 operações de escrita ou 300 segundos.

A estagnação limitada oferece uma ordem global total fora da "janela velha". Quando um cliente realiza operações de leitura dentro de uma região que aceita escritas, as garantias dadas pela consistência limitada são idênticas às garantias pela forte consistência. À medida que a janela de estagnação se aproxima para qualquer tempo ou atualizações, o que estiver mais perto, o serviço irá acelerar novas gravações para permitir que a replicação se apanhe e honre a garantia de consistência.

Dentro da janela de estagnação, a estagnação limitada fornece as seguintes garantias de consistência:

- Consistência para clientes na mesma região para uma conta com região de escrita única = Forte
- Consistência para clientes em diferentes regiões para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes que escrevem para uma única região para uma conta com múltiplas regiões de escrita = Prefix consistente
- Consistência para clientes que escrevem em diferentes regiões para uma conta com múltiplas regiões de escrita = Eventual

  A estagnação limitada é frequentemente escolhida por aplicações distribuídas globalmente que esperam baixas latências de escrita, mas requerem total garantia de ordem global. A estagnação limitada é ótima para aplicações com colaboração e partilha de grupos, ticker de stock, publicação-subscrever/fila, etc. O gráfico a seguir ilustra a consistência deslimícula com notas musicais. Após a escrita dos dados para a região "West US 2", as regiões "Leste DOS EUA 2" e "Austrália Oriental" lêem o valor escrito baseado no tempo de atraso máximo configurado ou nas operações máximas:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Ilustração do nível de consistência deslimido":::

### <a name="session-consistency"></a>Consistência de sessão

Na consistência da sessão, dentro de uma única sessão de clientes as leituras são garantidas para honrar as consistentes-prefixos, leituras monotónicas, escritas monótonas, leituras-suas-escritas e garantias de leitura de leituras de escrita. Isto pressupõe uma única sessão de "escritor" ou partilhar o símbolo da sessão para vários escritores.

Os clientes fora da sessão que realizam escritas verão as seguintes garantias:

- Consistência para clientes na mesma região para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes em diferentes regiões para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes que escrevem para uma única região para uma conta com múltiplas regiões de escrita = Prefix consistente
- Consistência para clientes que escrevem em várias regiões para uma conta com múltiplas regiões de escrita = Eventual

  A consistência da sessão é o nível de consistência mais utilizado tanto para uma região como para aplicações distribuídas globalmente. Fornece latências escritas, disponibilidade e produção de leitura comparáveis às de eventual consistência, mas também fornece garantias de consistência que se adequam às necessidades das aplicações escritas para operar no contexto de um utilizador. O gráfico que se segue ilustra a consistência da sessão com notas musicais. O "West US 2 writer" e o "West US 2 reader" estão a usar a mesma sessão (Sessão A) para que ambos leiam os mesmos dados ao mesmo tempo. Enquanto a região "Australia East" está a usar a "Sessão B" por isso, recebe dados mais tarde, mas na mesma ordem que os escritos.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Ilustração do nível de consistência da sessão":::

### <a name="consistent-prefix-consistency"></a>Consistência de prefixo consistente

Na opção de prefixo consistente, as atualizações que são devolvidas contêm algum prefixo de todas as atualizações, sem lacunas. Garantias consistentes de nível de consistência prefixo que lê nunca ver escritos fora de ordem.

Se as gravações forem realizadas na `A, B, C` ordem, então um cliente vê ou `A` , ou , mas nunca `A,B` `A,B,C` permutações fora de ordem como `A,C` ou `B,A,C` . O Prefixo consistente fornece latências de escrita, disponibilidade e produção de leitura comparáveis às de eventual consistência, mas também fornece garantias de encomenda que se adequam às necessidades dos cenários em que a ordem é importante.

Abaixo estão as garantias de consistência para Prefix consistente:

- Consistência para clientes na mesma região para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes em diferentes regiões para uma conta com região de escrita única = Prefix consistente
- Consistência para clientes que escrevem para uma única região para uma conta com região de escrita múltipla = Prefix consistente
- Consistência para clientes que escrevem em várias regiões para uma conta com múltiplas regiões de escrita = Eventual

O gráfico a seguir ilustra a consistência do prefixo de consistência com notas musicais. Em todas as regiões, as leituras nunca vêem fora de ordem escreve:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Ilustração de prefixo consistente":::

### <a name="eventual-consistency"></a>Consistência eventual

Em eventual consistência, não há garantia de pedido para leituras. Na ausência de escritas adicionais, as réplicas acabam por convergir.  
A consistência eventual é a forma mais fraca de consistência porque um cliente pode ler os valores que são mais antigos do que os que tinha lido antes. A eventual consistência é ideal quando a aplicação não requer quaisquer garantias de encomenda. Exemplos incluem contagem de retweets, gostos ou comentários não roscados. O gráfico a seguir ilustra a eventual consistência com notas musicais.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="viIllustration de eventual consistência":::

## <a name="consistency-guarantees-in-practice"></a>Garantias de consistência na prática

Na prática, pode muitas vezes obter garantias de consistência mais fortes. As garantias de consistência para uma operação de leitura correspondem à frescura e ao pedido da base de dados que solicita. A consistência de leitura está ligada à ordem e propagação das operações de escrita/atualização.  

Se não houver operações de escrita na base de dados, uma operação de leitura com **eventuais,** **sessão** ou níveis consistentes de consistência **prefixo** é suscetível de produzir os mesmos resultados que uma operação de leitura com forte nível de consistência.

Se a sua conta Azure Cosmos estiver configurada com um nível de consistência diferente da forte consistência, pode descobrir a probabilidade de os seus clientes ficarem fortes e consistentes com leituras para as suas cargas de trabalho, olhando para a métrica *Probabilisticamente Bounded Staleness* (PBS). Esta métrica está exposta no portal Azure, para saber mais, consulte a [métrica de Staleness (PBS) (Monitor Probabilisisticamente Bounded).](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

A estagnação deslimaturada probabilística mostra como a sua eventual consistência é a sua eventual consistência. Esta métrica fornece uma visão de quantas vezes você pode obter uma consistência mais forte do que o nível de consistência que você tem atualmente configurado na sua conta Azure Cosmos. Por outras palavras, pode-se ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de escrita e leitura.

## <a name="consistency-levels-and-latency"></a>Níveis de consistência e latência

A latência lida para todos os níveis de consistência é sempre garantida ser inferior a 10 milissegundos no percentil 99. A latência média de leitura, no percentil 50, é tipicamente de 4 milissegundos ou menos.

A latência da escrita para todos os níveis de consistência é sempre garantida ser inferior a 10 milissegundos no percentil 99. A latência média da escrita, no percentil 50, é geralmente de 5 milissegundos ou menos. As contas da Azure Cosmos que abrangem várias regiões e estão configuradas com forte consistência são uma exceção a esta garantia.

### <a name="write-latency-and-strong-consistency"></a>Escreva latência e forte consistência

Para as contas de Azure Cosmos configuradas com forte consistência com mais de uma região, a latência da escrita é igual a duas vezes tempo de ida e volta (RTT) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no percentil 99. A alta rede RTT entre as regiões traduzir-se-á numa maior latência para os pedidos de DB da Cosmos, uma vez que a forte consistência só completa uma operação depois de garantir que foi comprometida com todas as regiões dentro de uma conta.

A latência exata do RTT é uma função da distância de velocidade da luz e da topologia de rede Azure. A rede Azure não fornece quaisquer SLAs de latência para o RTT entre duas regiões do Azure, no entanto publica [estatísticas de latência de ida e volta da rede Azure](../networking/azure-network-latency.md). Para a sua conta Azure Cosmos, as latências de replicação são exibidas no portal Azure. Pode utilizar o portal Azure (vá à lâmina Métricas, selecione 'Aba de Consistência' para monitorizar as latências de replicação entre várias regiões que estão associadas à sua conta Azure Cosmos.

> [!IMPORTANT]
> A forte consistência das contas com regiões com uma extensão superior a 8000 km é bloqueada por defeito devido à elevada latência escrita. Para ativar esta capacidade contacte o suporte.

## <a name="consistency-levels-and-throughput"></a>Níveis de consistência e produção

- Para uma estagnação forte e limitada, as leituras são feitas contra duas réplicas num conjunto de réplicas (quórum minoritário) para fornecer garantias de consistência. Sessão, prefixo consistente e, eventualmente, leituras de réplica única. O resultado é que, para o mesmo número de unidades de pedido, ler a produção para uma estagnação forte e limitada é metade dos outros níveis de consistência.

- Para um determinado tipo de operação de escrita, como inserir, substituir, aumentar e apagar, o resultado de escrita para unidades de pedido é idêntico para todos os níveis de consistência.

|**Nível de Consistência**|**Leituras de quórum**|**Quorum escreve**|
|--|--|--|
|**Forte**|Minoria Local|Maioria Global|
|**Estagnação limitada**|Minoria Local|Maioria Local|
|**Sessão**|Réplica única (token de sessão)|Maioria Local|
|**Prefixo consistente**|Réplica única|Maioria Local|
|**Eventual**|Réplica única|Maioria Local|

> [!NOTE]
> O custo ru/s das leituras para as leituras da minoria local é o dobro dos níveis de consistência mais fracos porque as leituras são feitas a partir de duas réplicas para fornecer garantias de consistência para a estagnação forte e limitada.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Níveis de consistência e durabilidade dos dados

Dentro de um ambiente de base de dados distribuído globalmente, existe uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma paralisação a nível regional. À medida que desenvolve o seu plano de continuidade de negócios, precisa entender o tempo máximo aceitável antes que a aplicação recupere totalmente após um evento disruptivo. O tempo necessário para uma aplicação de recuperação total é conhecido como **objetivo de tempo de recuperação** **(RTO).** Também precisa entender o período máximo de atualizações de dados recentes que a aplicação pode tolerar perder ao recuperar após um evento disruptivo. O período de tempo das atualizações que pode perder é conhecido como **objetivo de ponto de recuperação** **(RPO).**

O quadro abaixo define a relação entre o modelo de consistência e a durabilidade dos dados na presença de uma paragem generalizada da região. É importante notar que num sistema distribuído, mesmo com forte consistência, é impossível ter uma base de dados distribuída com um RPO e RTO de zero devido ao [Teorema da PAC.](https://en.wikipedia.org/wiki/CAP_theorem)

|**Regiões(s)**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Regiões de escrita únicas ou múltiplas|Qualquer nível de consistência|< 240 Minutos|<1 Semana|
|>1|Região de escrita única|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|>1|Região de escrita única|Estagnação Limitada|*K*  &  *T*|< 15 minutos|
|>1|Região de escrita única|Forte|0|< 15 minutos|
|>1|Múltiplas regiões de escrita|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|>1|Múltiplas regiões de escrita|Estagnação Limitada|*K*  &  *T*|0|

*K* = O número de *versões "K"* (isto é, atualizações) de um item.

*T* = O intervalo de tempo *"T"* desde a última atualização.

Para uma única conta de região, o valor mínimo de *K* e *T* é de 10 operações de escrita ou 5 segundos. Para contas multi-regiões, o valor mínimo de *K* e *T* é de 100.000 operações de escrita ou 300 segundos. Isto define o RPO mínimo para os dados quando se utiliza a Estagnação Limitada.

## <a name="strong-consistency-and-multiple-write-regions"></a>Forte consistência e múltiplas regiões de escrita

As contas cosmos configuradas com múltiplas regiões de escrita não podem ser configuradas para uma forte consistência, uma vez que não é possível que um sistema distribuído forneça um RPO de zero e um RTO de zero. Além disso, não existem benefícios de latência de escrita na utilização de uma forte consistência com múltiplas regiões de escrita, porque uma escrita para qualquer região deve ser replicada e comprometida com todas as regiões configuradas dentro da conta. Isto resulta na mesma latência escrita que uma única conta de uma região de escrita.

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

- [Configurar o nível de consistência predefinido](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Substituir o nível de consistência predefinido](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
