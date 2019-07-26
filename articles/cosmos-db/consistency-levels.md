---
title: Níveis de consistência no Azure Cosmos DB
description: Azure Cosmos DB tem cinco níveis de consistência para o ajudar a equilibrar eventual consistência, disponibilidade e latência vantagens e desvantagens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 395b7bc31377fd771549a399032bad9d951ec804
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384916"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de consistência no Azure Cosmos DB

Bases de dados distribuídas que dependem de replicação para elevada disponibilidade, latência baixa ou ambos, tornam o compromisso fundamental entre a consistência de leitura versus disponibilidade, latência e débito. A maioria dos bancos de dados distribuídos comercialmente disponíveis solicita que os desenvolvedores escolham entre os dois modelos de consistência extremos: consistência *forte* e consistência *eventual* . O transação atômica ou o modelo de consistência forte é o padrão ouro de programação de dados. Mas ele adiciona um preço de latência mais alta (em estado estacionário) e disponibilidade reduzida (durante falhas). Por outro lado, a consistência eventual oferece maior disponibilidade e melhor desempenho, mas torna difícil programar aplicativos. 

O Azure Cosmos DB se aproxima de consistência de dados como uma gama de opções em vez de dois extremos. A consistência forte e a consistência eventual estão nas extremidades do espectro, mas há muitas opções de consistência ao longo do espectro. Os desenvolvedores podem usar essas opções para fazer compensações granulares e completas em relação à alta disponibilidade e ao desempenho. 

Com o Azure Cosmos DB, os desenvolvedores podem escolher entre cinco modelos de consistência bem definidos do espetro de consistência. Do mais forte para o mais relaxado, os modelos incluem *forte*, desatualização *limitada*, *sessão*, *prefixo consistente*e consistência *eventual* . Os modelos são bem definidos e intuitivos e podem ser usados para cenários do mundo real específicos. Cada modelo fornece compensações de [desempenho e disponibilidade](consistency-levels-tradeoffs.md) e é apoiado pelos SLAs. A imagem a seguir mostra os diferentes níveis de consistência como um espectro.

![Consistência como um espectro](./media/consistency-levels/five-consistency-levels.png)

Os níveis de consistência são independentes de região e são garantidos para todas as operações, independentemente da região da qual as leituras e gravações são atendidas, o número de regiões associadas à sua conta do Azure Cosmos ou se sua conta está configurada com um único ou várias regiões de gravação.

## <a name="scope-of-the-read-consistency"></a>Âmbito de consistência de leitura

Consistência de leitura aplica-se a uma única operação de leitura no âmbito dentro de um intervalo de chave de partição ou de uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

Pode configurar o nível predefinido de consistência na sua conta do Cosmos do Azure em qualquer altura. O nível de consistência padrão configurado em sua conta se aplica a todos os bancos de dados e contêineres do Azure Cosmos sob essa conta. Todas as leituras e consultas emitidas relativamente de um contentor ou uma base de dados, utilize o nível de consistência especificado por predefinição. Para obter mais informações, consulte como [configurar o nível de consistência predefinido](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias de associadas com níveis de consistência

Os SLAs abrangentes fornecidos pela garantia do Azure Cosmos DB que 100 por cento de pedidos de leitura cumprem a garantia de consistência para qualquer nível de consistência que escolher. Uma solicitação de leitura cumpre a SLA de consistência se todas as garantias de consistência associadas com o nível de consistência são cumpridas. As definições precisas dos cinco níveis de consistência no Azure Cosmos DB usando a linguagem de especificação TLA + são fornecidas no repositório GitHub [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) .

A semântica dos níveis de cinco consistência é descrita aqui:

- **Forte**: A consistência forte oferece uma garantia transação atômica. Transação atômica refere-se ao fornecimento de solicitações simultaneamente. As leituras são garantidas para devolver a versão mais recente de compromisso de um item. Um cliente nunca vê uma gravação não consolidada ou parcial. Os utilizadores garantidos para ler a escrita mais recente confirmada.

- Desatualização **limitada**: As leituras são garantidas de honrar a garantia de prefixo consistente. As leituras podem atrasar por trás das gravações por no máximo *"K"* versões (ou seja, "Atualizações") de um item ou por um intervalo de tempo *"T"* . Em outras palavras, quando você escolhe a desatualização limitada, a "desatualização" pode ser configurada de duas maneiras: 

  * O número de versões (*K*) do item
  * O intervalo de tempo (*T*) pelo qual as leituras podem atrasar por trás das gravações 

  Estagnação limitada ofertas total global do pedido, exceto dentro da "janela limitada". As garantias de leitura monotónica existem dentro de uma região dentro e fora da janela de limitada. A consistência forte tem a mesma semântica que aquela oferecida pela desatualização limitada. A janela de envelhecimento é igual a zero. Estagnação limitada é também referida como o atraso de tempo de transação atómica. Quando um cliente executa operações de leitura em uma região que aceita gravações, as garantias fornecidas pela consistência de desatualização limitada são idênticas às garantias pela consistência forte.

- **Sessão**:  Em uma única sessão de cliente, as leituras têm a garantia de honrar o prefixo consistente (supondo uma única sessão de "gravador"), leituras de monotônico, gravações de monotônico, leitura-gravação-gravações e garantias de gravação a seguir-leituras. Os clientes fora da sessão que executam gravações verão a consistência eventual.

- **Prefixo consistente**: As atualizações que são retornadas contêm algum prefixo de todas as atualizações, sem intervalos. O nível de consistência de prefixo consistente garante que as leituras nunca vejam gravações fora de ordem.

- **Eventual**: Não há garantia de classificação para leituras. Na ausência de qualquer escrita adicional, as réplicas no final de convergência.

## <a name="consistency-levels-explained-through-baseball"></a>Níveis de consistência explicadas através do basebol

Por exemplo, vejamos um cenário de jogo de beisebol. Imagine uma sequência de escritas que representam a pontuação de um jogo de beisebol. A pontuação de linha de inning por inning está descrita com o [replicados a consistência dos dados através do basebol](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) paper. Este jogo de beisebol hipotético é atualmente no meio o sétimo inning. É a stretch do sétima – inning. Os visitantes estão por trás com uma pontuação de 2 a 5, conforme mostrado abaixo:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Execuções** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Visitantes** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **página inicial** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Um contêiner Cosmos do Azure contém os totais de execução para os visitantes e as equipes residenciais. Enquanto o jogo estiver em curso, o diferentes ler de garantias podem resultar em leitura pontuações diferentes de clientes. A tabela seguinte lista o conjunto completo de classificações que pode ser retornado ao ler dos visitantes e pontuações principais com cada um das garantias de consistência de cinco. Classificação dos visitantes é listada em primeiro lugar. Diferentes possíveis valores de retorno são separados por vírgulas.

| **Nível de consistência** | **Pontuações (visitantes, início)** |
| - | - |
| **Forte** | 2 a 5 |
| **Estagnação limitada** | Pontuações que estão no máximo um inning desatualizado: 2-3, 2-4, 2-5 |
| **Sessão** | <ul><li>Para o gravador: 2 a 5</li><li> Para qualquer outra pessoa que não seja o gravador: 0 de 0, 0-1, 0-2, 0 a 3, 4 de 0, 0-5, 1-0, 1-1, 1 a 2, 1 a 3, 1 a 4, 1 a 5, 2-0, 2-1, 2 de 2, 2 e 3, 2 a 4, 2 a 5</li><li>Depois de ler 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Prefixo consistente** | 0 de 0, 0-1, 1-1, 1 a 2, 1 a 3, 2 e 3, 2 a 4, 2 a 5 |
| **Eventual** | 0 de 0, 0-1, 0-2, 0 a 3, 4 de 0, 0-5, 1-0, 1-1, 1 a 2, 1 a 3, 1 a 4, 1 a 5, 2-0, 2-1, 2 de 2, 2 e 3, 2 a 4, 2 a 5 |

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre os conceitos de consistência, leia os artigos seguintes:

- [Alto nível TLA + especificações para os níveis de cinco consistência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replicados dados consistência explicado por meio de Beisebol (vídeo) por Doug Tiago](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replicados dados consistência explicado por meio de Beisebol (documento técnico) por Doug Tiago](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Garantias de sessão para os dados replicados consistentes tem rigidez](https://dl.acm.org/citation.cfm?id=383631)
- [Compensações de consistência no design de sistemas de banco de dados distribuído moderno: CAP é apenas parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilístico estagnação limitada (PBS) para práticos Quorums parciais](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventualmente consistente - Revisitada](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os níveis de consistência no Azure Cosmos DB, leia os artigos seguintes:

* [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
* [Níveis de consistência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Configurar o nível de consistência predefinida](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Substituir o nível de consistência predefinido](how-to-manage-consistency.md#override-the-default-consistency-level)

