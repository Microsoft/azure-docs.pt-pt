---
title: Níveis de consistência no Azure Cosmos DB
description: Azure Cosmos DB tem cinco níveis de consistência para o ajudar a equilibrar eventual consistência, disponibilidade e latência vantagens e desvantagens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: b43fe513b15d55ee595acaa6733d96cdb58f4e83
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294516"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de consistência no Azure Cosmos DB

Bases de dados distribuídas que dependem de replicação para elevada disponibilidade, latência baixa ou ambos, tornam o compromisso fundamental entre a consistência de leitura versus disponibilidade, latência e débito. A maioria dos comercialmente disponíveis bases de dados distribuídas perguntar aos desenvolvedores escolher entre os dois modelos de consistência extreme: *forte* consistência e *eventual* consistência. O  [transação atómica](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) ou o modelo de consistência forte é o padrão-ouro da programação de dados. Mas adiciona um preço de latência superior (em estado de repouso) e disponibilidade reduzida (durante falhas). Por outro lado, a consistência eventual oferece maior disponibilidade e um melhor desempenho, mas fica difícil de se programar aplicativos. 

O Azure Cosmos DB se aproxima de consistência de dados como uma gama de opções em vez de dois extremos. Consistência forte e consistência eventual estão nas extremidades do espectro, mas há muitas escolhas de consistência ao longo do espetro. Os programadores podem utilizar estas opções para fazer escolhas precisas e granulares compromissos em relação a elevada disponibilidade e desempenho. 

Com o Azure Cosmos DB, os desenvolvedores podem escolher entre cinco modelos de consistência bem definidos do espetro de consistência. No mais forte para o mais Relaxada, os modelos incluem *forte*, *estagnação limitada*, *sessão*, *prefixo consistente*e o *eventual* consistência. Os modelos são bem definidas e intuitiva e podem ser utilizados para cenários específicos do mundo real. Cada modelo fornece [vantagens e desvantagens de desempenho e disponibilidade](consistency-levels-tradeoffs.md) apoiado pelos SLAs. A imagem seguinte mostra os níveis de consistência diferentes, como um espectro.

![Consistência como um espectro](./media/consistency-levels/five-consistency-levels.png)

Os níveis de consistência são agnósticos relativamente à região e são garantidos para todas as operações, independentemente da região a partir do qual as leituras e gravações são enviadas e o número de regiões à sua conta do Cosmos do Azure, ou se a sua conta está configurada com um único ou várias regiões de escrita.

## <a name="scope-of-the-read-consistency"></a>Âmbito de consistência de leitura

Consistência de leitura aplica-se a uma única operação de leitura no âmbito dentro de um intervalo de chave de partição ou de uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

Pode configurar o nível predefinido de consistência na sua conta do Cosmos do Azure em qualquer altura. O nível de consistência predefinido configurado na sua conta se aplica a todas as bases de dados do Azure Cosmos DB e contentores nessa conta. Todas as leituras e consultas emitidas relativamente de um contentor ou uma base de dados, utilize o nível de consistência especificado por predefinição. Para obter mais informações, consulte como [configurar o nível de consistência predefinido](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias de associadas com níveis de consistência

Os SLAs abrangentes fornecidos pela garantia do Azure Cosmos DB que 100 por cento de pedidos de leitura cumprem a garantia de consistência para qualquer nível de consistência que escolher. Uma solicitação de leitura cumpre a SLA de consistência se todas as garantias de consistência associadas com o nível de consistência são cumpridas. As definições de precisas dos níveis de cinco consistência no Azure Cosmos DB ao utilizar o [linguagem de especificação TLA +](https://lamport.azurewebsites.net/tla/tla.html) são fornecidos na [do azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) repositório do GitHub. 

A semântica dos níveis de cinco consistência é descrita aqui:

- **Forte**: Consistência forte oferece uma [transação atómica](https://aphyr.com/posts/313-strong-consistency-models) garantir. As leituras são garantidas para devolver a versão mais recente de compromisso de um item. Um cliente nunca vê uma gravação não consolidada ou parcial. Os utilizadores garantidos para ler a escrita mais recente confirmada.

- **Estagnação limitada**: As leituras são garantidas para honrar a garantia de prefixo consistente. As poderão desfasamento de leituras escritas por no máximo versões de "K" (ou seja, "atualizações") de um item ou por intervalo de tempo de "t". Ao escolher estagnação limitada, o "envelhecimento" pode ser configurado de duas formas: 

  * O número de versões (K) do item
  * O intervalo de tempo (t) através do qual as poderão desfasamento de leituras as gravações 

  Estagnação limitada ofertas total global do pedido, exceto dentro da "janela limitada". As garantias de leitura monotónica existem dentro de uma região dentro e fora da janela de limitada. Consistência forte tem a mesma semântica que as oferecidas pelo estagnação limitada. A janela de envelhecimento é igual a zero. Estagnação limitada é também referida como o atraso de tempo de transação atómica. Quando um cliente executa operações de leitura numa região que aceita escritas, as garantias fornecidas pelo consistência de estagnação limitada são idênticas essas garantias com a consistência forte.

- **Sessão**: As leituras são garantidas para honrar o prefixo consistente (supondo que uma sessão de escritor"única"), leituras monotónicas, escritas monotónica, garantias de leitura-your-escritas e escrita de acordo com leituras. Consistência da sessão é confinada a uma sessão de cliente.

- **Prefixo consistente**: Atualizações que são devolvidas contêm alguns prefixos de todas as atualizações, sem intervalos. Prefixo consistente garante que leituras nunca veem escritas fora de ordem.

- **Eventual**: Não há nenhuma garantia de ordenação para leituras. Na ausência de qualquer escrita adicional, as réplicas no final de convergência.

## <a name="consistency-levels-explained-through-baseball"></a>Níveis de consistência explicadas através do basebol

Por exemplo, vejamos um cenário de jogo de beisebol. Imagine uma sequência de escritas que representam a pontuação de um jogo de beisebol. A pontuação de linha de inning por inning está descrita com o [replicados a consistência dos dados através do basebol](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) paper. Este jogo de beisebol hipotético é atualmente no meio o sétimo inning. É a stretch do sétima – inning. Os visitantes que estão por detrás com uma classificação de 2 a 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Execuções** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Visitantes** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **página inicial** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Um contentor do Azure Cosmos DB mantém dos visitantes e equipe principal execute totais. Enquanto o jogo estiver em curso, o diferentes ler de garantias podem resultar em leitura pontuações diferentes de clientes. A tabela seguinte lista o conjunto completo de classificações que pode ser retornado ao ler dos visitantes e pontuações principais com cada um das garantias de consistência de cinco. Classificação dos visitantes é listada em primeiro lugar. Diferentes possíveis valores de retorno são separados por vírgulas.

| **Nível de consistência** | **Pontuações** |
| - | - |
| **Forte** | 2 a 5 |
| **Estagnação limitada** | Classificações que têm mais de um inning desatualizado: 2-3, 2-4, 2-5 |
| **Sessão** | <ul><li>Para o gravador de: 2 a 5</li><li> Para qualquer pessoa que não seja o escritor: 0 de 0, 0-1, 0-2, 0 a 3, 4 de 0, 0-5, 1-0, 1-1, 1 a 2, 1 a 3, 1 a 4, 1 a 5, 2-0, 2-1, 2 de 2, 2 e 3, 2 a 4, 2 a 5</li><li>Depois de ler 1 a 3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Prefixo consistente** | 0 de 0, 0-1, 1-1, 1 a 2, 1 a 3, 2 e 3, 2 a 4, 2 a 5 |
| **Eventual** | 0 de 0, 0-1, 0-2, 0 a 3, 4 de 0, 0-5, 1-0, 1-1, 1 a 2, 1 a 3, 1 a 4, 1 a 5, 2-0, 2-1, 2 de 2, 2 e 3, 2 a 4, 2 a 5 |

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre os conceitos de consistência, leia os artigos seguintes:

- [Alto nível TLA + especificações para os níveis de cinco consistência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replicados dados consistência explicado por meio de Beisebol (vídeo) por Doug Tiago](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replicados dados consistência explicado por meio de Beisebol (documento técnico) por Doug Tiago](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Garantias de sessão para os dados replicados consistentes tem rigidez](https://dl.acm.org/citation.cfm?id=383631)
- [Vantagens e desvantagens de consistência no moderno distribuídas de Design de sistemas de banco de dados: Extremidade é apenas uma parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilístico estagnação limitada (PBS) para práticos Quorums parciais](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventualmente consistente - Revisitada](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os níveis de consistência no Azure Cosmos DB, leia os artigos seguintes:

* [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
* [Níveis de consistência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Configurar o nível de consistência predefinida](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Substituir o nível de consistência predefinido](how-to-manage-consistency.md#override-the-default-consistency-level)

