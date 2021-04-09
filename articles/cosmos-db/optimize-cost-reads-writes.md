---
title: Otimizar o custo dos seus pedidos na Azure Cosmos DB
description: Este artigo explica como otimizar os custos ao emitir pedidos na Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 36ecef007e10f9a090dbabc8b5a91fd473930141
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633857"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Otimizar o custo do pedido na Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo descreve como ler e escrever pedidos se traduzem em [Unidades de Pedido](request-units.md) e como otimizar o custo destes pedidos. As operações de leitura incluem leituras de pontos e consultas. As operações de escrita incluem inserção, substituição, eliminação e redução de itens.

A Azure Cosmos DB oferece um rico conjunto de operações de base de dados que operam nos itens dentro de um contentor. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa Unidade de Pedido (RU) como uma única medida para os recursos necessários para realizar várias operações de base de dados para atender um pedido.

## <a name="measuring-the-ru-charge-of-a-request"></a>Medição da taxa RU de um pedido

É importante medir a carga RU dos seus pedidos para compreender o seu custo real e também avaliar a eficácia das suas otimizações. Você pode obter este custo usando o portal Azure ou inspecionando a resposta enviada de Azure Cosmos DB através de um dos SDKs. Consulte [a carga da unidade de pedido em Azure Cosmos DB](find-request-unit-charge.md) para obter instruções detalhadas sobre como o conseguir.

## <a name="reading-data-point-reads-and-queries"></a>Dados de leitura: leituras e consultas pontuais

As operações de leitura em Azure Cosmos DB são normalmente encomendadas de mais rápido/mais eficiente para mais lento/menos eficiente em termos de consumo de RU da seguinte forma:  

* Leituras de pontos (procura de chave/valor numa única chave de iD de item e chave de partição).
* Consulta com uma cláusula de filtro dentro de uma única chave de partição.
* Consulta sem uma cláusula de filtro de igualdade ou alcance em qualquer propriedade.
* Consulta sem filtros.

### <a name="role-of-the-consistency-level"></a>Papel do nível de consistência

Ao utilizar os níveis de consistência **forte** ou [limitados,](consistency-levels.md)o custo RU de qualquer operação de leitura (leitura ou consulta de ponto) é duplicado. 

### <a name="point-reads"></a>Leituras de pontos

O único fator que afeta a carga RU de um ponto lido (para além do nível de consistência utilizado) é o tamanho do item recuperado. A tabela a seguir mostra o custo ru de leituras de pontos para itens que são de tamanho 1 KB e 100 KB.

| **Tamanho do item** | **Custo de um ponto lido** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RUs |

Uma vez que as leituras de pontos (as pesquisas de chave/valor no ID do item) são o tipo de leitura mais eficiente, deve certificar-se de que o seu ID de artigo tem um valor significativo para que possa obter os seus itens com um ponto lido (em vez de uma consulta) quando possível.

### <a name="queries"></a>Consultas

As unidades de pedido para consultas dependem de uma série de fatores. Por exemplo, o número de itens Azure Cosmos carregados/devolvidos, o número de procuras contra o índice, o tempo de compilação de consultas, etc. detalhes. A Azure Cosmos DB garante que a mesma consulta quando executada nos mesmos dados consumirá sempre o mesmo número de unidades de pedido, mesmo com execuções repetidas. O perfil de consulta utilizando métricas de execução de consulta dá-lhe uma boa ideia de como as unidades de pedido são gastas.  

Em alguns casos, você pode ver uma sequência de 200 e 429 respostas, e unidades de pedido variável em uma execução paged de consultas, isto é, porque as consultas serão executadas o mais rápido possível com base nas RUs disponíveis. Pode ver uma execução de consulta a partir-se em várias páginas/viagens de ida e volta entre o servidor e o cliente. Por exemplo, 10.000 itens podem ser devolvidos como várias páginas, cada um carregado com base no cálculo realizado para essa página. Quando se soma nestas páginas, deve obter o mesmo número de RUs que obteria para toda a consulta.

#### <a name="metrics-for-troubleshooting-queries"></a>Métricas para consultas de resolução de problemas

O desempenho e a produção consumidas por consultas (incluindo funções definidas pelo utilizador) dependem principalmente do corpo da função. A maneira mais fácil de descobrir quanto tempo a execução de consulta é gasta na UDF e o número de RUs consumidos, é permitindo as métricas de consulta. Se utilizar o .NET SDK, aqui estão as métricas de consulta de amostra devolvidas pelo SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Melhores práticas para permitir a otimização de consultas 

Considere as seguintes boas práticas ao otimizar consultas por custo:

* **Colocar vários tipos de entidades**

   Tente alotear vários tipos de entidades num único ou menor número de contentores. Este método beneficia não só do ponto de vista dos preços, mas também da execução de consultas e transações. As consultas são procuradas num único contentor; e as transações atómicas sobre vários registos através de procedimentos/gatilhos armazenados são telescópios numa chave de partição dentro de um único recipiente. As entidades de colocação dentro do mesmo contentor podem reduzir o número de viagens de ida e volta de rede para resolver relações entre registos. Assim, aumenta o desempenho de ponta a ponta, permite transações atómicas em vários registos para um conjunto de dados maior, e como resultado reduz os custos. Se a colocação de vários tipos de entidades num único ou menor número de contentores é difícil para o seu cenário, normalmente porque está a migrar uma aplicação existente e não quer fazer alterações de código - deve então considerar o fornecimento de produção ao nível da base de dados.  

* **Medida e sintonização para unidades de pedido mais baixas/segunda utilização**

   A complexidade de uma consulta tem impacto no número de unidades de pedido (RUs) consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem. Todos estes fatores influenciam o custo das operações de consulta. 

A Azure Cosmos DB proporciona um desempenho previsível em termos de produção e latência utilizando um modelo de produção provisionado. O valor previsto é representado em termos de [Unidades](request-units.md) de Pedido por segundo, ou RU/s. Uma Unidade de Pedido (RU) é uma abstração lógica sobre recursos computativos tais como CPU, memória, IO, etc. que são obrigados a realizar um pedido. A produção prevista (RUs) é reservada e dedicada ao seu contentor ou base de dados para fornecer produção e latência previsíveis. A produção a provisionada permite que a Azure Cosmos DB proporcione um desempenho previsível e consistente, baixa latência garantida e elevada disponibilidade em qualquer escala. As unidades de pedido representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos uma aplicação necessita.

A taxa de pedido devolvida no cabeçalho do pedido indica o custo de uma determinada consulta. Por exemplo, se uma consulta devolver 1000 itens 1-KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de taxa limitando os pedidos subsequentes. Para mais informações, consulte o artigo [das unidades de pedido](request-units.md) e a calculadora da unidade de pedido.

## <a name="writing-data"></a>Escrever dados

O custo ru de escrever um item depende de:

- O tamanho do item.
- O número de imóveis abrangidos pela [política de indexação](index-policy.md) precisava de ser indexado.

Inserir um item de 1 KB sem indexar custa cerca de ~5,5 RUs. A substituição de um artigo custa duas vezes a carga necessária para inserir o mesmo item.

### <a name="optimizing-writes"></a>Otimização de escritos

A melhor forma de otimizar o custo ru das operações de escrita é direitos de direito aos seus itens e ao número de propriedades que são indexadas.

- Armazenar itens muito grandes em Azure Cosmos DB resulta em altas cargas RU e pode ser considerado como um anti-padrão. Em particular, não guarde conteúdo binário ou grandes pedaços de texto que não precisa de consultar. Uma boa prática é colocar este tipo de dados no [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) e armazenar uma referência (ou link) para a bolha no item que escreve para a Azure Cosmos DB.
- Otimizar a sua política de indexação apenas para indexar as propriedades em que as suas consultas filtram pode fazer uma enorme diferença nas RUs consumidas pelas suas operações de escrita. Ao criar um novo recipiente, a política de indexação padrão indexa cada uma das propriedades encontradas nos seus itens. Embora este seja um bom padrão para atividades de desenvolvimento, é altamente recomendado reavaliar e [personalizar a sua política](how-to-manage-indexing-policy.md) de indexação quando vai para a produção ou quando a sua carga de trabalho começa a receber tráfego significativo.

Ao realizar a ingestão a granel de dados, recomenda-se também a utilização da [biblioteca de executores a granel Azure Cosmos DB,](bulk-executor-overview.md) uma vez que foi concebida para otimizar o consumo de RU de tais operações. Opcionalmente, também pode utilizar [a Azure Data Factory,](../data-factory/introduction.md) que é construída nessa mesma biblioteca.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [a capacidade reservada da Azure Cosmos DB](cosmos-db-reserved-capacity.md)
