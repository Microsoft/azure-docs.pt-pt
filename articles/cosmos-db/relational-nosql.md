---
title: Compreender as diferenças entre Azure Cosmos DB NoSQL e bases de dados relacionais
description: Este artigo enumera as diferenças entre noSQL e bases de dados relacionais
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088476"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Compreender as diferenças entre as bases de dados NoSQL e as bases de dados relacionais
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo enumerará alguns dos principais benefícios das bases de dados NoSQL sobre bases de dados relacionais. Também discutiremos alguns dos desafios no trabalho com o NoSQL. Para uma análise aprofundada das diferentes lojas de dados existentes, veja o nosso artigo sobre [a escolha da loja de dados certa.](/azure/architecture/guide/technology-choices/data-store-overview)

## <a name="high-throughput"></a>Débito elevado

Um dos desafios mais óbvios na manutenção de um sistema de base de dados relacional é que a maioria dos motores relacionais aplicam fechaduras e fechos para impor [uma semântica ácida](https://en.wikipedia.org/wiki/ACID)rigorosa. Esta abordagem tem benefícios em termos de garantir um estado de dados consistente dentro da base de dados. No entanto, existem pesadas compensações no que diz respeito à concordância, à latência e à disponibilidade. Devido a estas restrições arquitetónicas fundamentais, volumes transacionais elevados podem resultar na necessidade de dados manuais. Implementar o fragmento manual pode ser um exercício demorado e doloroso.

Nestes cenários, [as bases de dados distribuídas](https://en.wikipedia.org/wiki/Distributed_database) podem oferecer uma solução mais escalável. No entanto, a manutenção ainda pode ser um exercício dispendioso e demorado. Os administradores podem ter de fazer um trabalho extra para garantir que a natureza distribuída do sistema seja transparente. Podem também ter de explicar a natureza "desligada" da base de dados.

[A Azure Cosmos DB](./introduction.md) simplifica estes desafios, sendo implantado em todo o mundo em todas as regiões do Azure. As gamas de partição são capazes de ser dinamicamente subdivididas para crescer perfeitamente a base de dados de acordo com a aplicação, mantendo simultaneamente uma elevada disponibilidade. A governação de recursos nativos de granulado e bem controlado e controlado facilita [garantias de latência surpreendentes](./consistency-levels.md#consistency-levels-and-latency) e desempenho previsível. A partilha é totalmente gerida, pelo que os administradores não precisam de escrever código ou gerir divisórias.

Se os seus volumes transacionais estiverem a atingir níveis extremos, como muitos milhares de transações por segundo, deve considerar uma base de dados NoSQL distribuída. Considere a Azure Cosmos DB para a máxima eficiência, facilidade de manutenção e redução do custo total de propriedade.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Backend" border="false":::

## <a name="hierarchical-data"></a>Dados hierárquicos

Há um número significativo de casos de utilização em que as transações na base de dados podem conter muitas relações entre pais e filhos. Estas relações podem crescer significativamente ao longo do tempo, e revelar-se difíceis de gerir. Formas de bases de [dados hierárquicas](https://en.wikipedia.org/wiki/Hierarchical_database_model) surgiram durante a década de 1980, mas não eram populares devido à ineficiência no armazenamento. Também perderam tração à medida que o [modelo relacional de Ted Codd](https://en.wikipedia.org/wiki/Relational_model) se tornou o padrão de facto usado por praticamente todos os sistemas de gestão de bases de dados mainstream.

No entanto, hoje em dia, a popularidade das bases de dados de estilo documental tem crescido significativamente. Estas bases de dados podem ser consideradas uma reinventação do paradigma hierárquico da base de dados, agora desinibida pelas preocupações em torno do custo de armazenamento de dados no disco. Como resultado, manter muitas relações complexas entre pais e filhos numa base de dados relacional poderia agora ser considerado um anti-padrão em comparação com as abordagens modernas orientadas para o documento.

O surgimento do [design orientado para objetos](https://en.wikipedia.org/wiki/Object-oriented_design), e o [desfasamento de impedância](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) que surge ao combiná-lo com modelos relacionais, também destaca um antipadrão nas bases de dados relacionais para determinados casos de uso. Custos de manutenção ocultos, mas muitas vezes significativos, podem surgir como resultado. Embora [as abordagens ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) tenham evoluído para atenuar parcialmente esta base de dados orientadas para os documentos, no entanto, a colidem muito melhor com abordagens orientadas para os objetos. Com esta abordagem, os desenvolvedores não são obrigados a ser comprometidos com os condutores orm, ou [motores de base de dados de OO específicos](https://en.wikipedia.org/wiki/Object_database)da linguagem. Se os seus dados contiver muitos relacionamentos entre pais e filhos e níveis profundos de hierarquia, talvez deva considerar a utilização de uma base de dados de documentos NoSQL, como a [API API API AZURE Cosmos DB SQL](./introduction.md).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="Backend":::

## <a name="complex-networks-and-relationships"></a>Redes e relacionamentos complexos

Ironicamente, dado o seu nome, as bases de dados relacionais apresentam uma solução menos do que ideal para modelar relações profundas e complexas. A razão para isso é que as relações entre entidades não existem efetivamente numa base de dados relacional. Precisam de ser calculadas em tempo de execução, com relações complexas que requerem juntas cartesianas para permitir o mapeamento usando consultas. Como resultado, as operações tornam-se exponencialmente mais caras em termos de computação à medida que as relações aumentam. Em alguns casos, uma base de dados relacional que tente gerir essas entidades tornar-se-á inutilizável.

Várias formas de bases de dados "Rede" surgiram durante o tempo em que as bases de dados relacionais emergiram, mas tal como nas bases de dados hierárquicas, estes sistemas lutaram para ganhar popularidade. A adoção lenta deveu-se à falta de casos de uso na época, e às ineficiências de armazenamento. Hoje em dia, os motores da base de dados de gráficos poderiam ser considerados um ressurgimento do paradigma da base de dados da rede. O principal benefício destes sistemas é que as relações são armazenadas como "cidadãos de primeira classe" dentro da base de dados. Assim, cruzar relações pode ser feito em tempo constante, em vez de aumentar a complexidade do tempo com cada novo produto de junção ou transversais.

Se estiver a manter uma rede complexa de relações na sua base de dados, talvez queira considerar uma base de dados de gráficos como a [API API AZure Cosmos DB Gremlin](./graph-introduction.md) para a gestão destes dados.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Backend":::

Azure Cosmos DB é um serviço de base de dados multi-modelo, que oferece uma projeção de API para todos os principais tipos de modelos NoSQL; Coluna-família, Documento, Gráfico e Valor-Chave. As camadas de API do documento [Gremlin (gráfico)](./gremlin-support.md) e SQL (Core) são totalmente interoperáveis. Isto tem benefícios para alternar entre diferentes modelos ao nível da programabilidade. As lojas de gráficos podem ser consultadas tanto em termos de transações complexas de rede como de transações modeladas como registos documentais na mesma loja.

## <a name="fluid-schema"></a>Esquema de fluido

Outra característica específica das bases de dados relacionais é que os esquemas devem ser definidos no momento da conceção. Isto tem benefícios em termos de integridade referencial e conformidade dos dados. No entanto, também pode ser restritivo à medida que a aplicação cresce. Responder a alterações no esquema através de modelos logicamente separados que partilham a mesma definição de tabela ou base de dados pode tornar-se complexo ao longo do tempo. Estes casos de utilização beneficiam frequentemente do facto de o esquema ser devolvido ao pedido de gestão por registo. Isto requer que a base de dados seja "esquema agnóstico" e permita que os registos sejam "auto-descrição" em termos dos dados contidos nos mesmos.

Se estiver a gerir dados cujas estruturas estão constantemente a mudar a uma taxa elevada, especialmente se as transações podem vir de fontes externas onde é difícil impor a conformidade através da base de dados, talvez queira considerar uma abordagem mais agnóstica usando um serviço de base de dados NoSQL gerido como o Azure Cosmos DB.

## <a name="microservices"></a>Microsserviços

O padrão [dos microserviços](https://en.wikipedia.org/wiki/Microservices) tem crescido significativamente nos últimos anos. Este padrão tem as suas raízes na [Arquitetura Orientada para o Serviço.](https://en.wikipedia.org/wiki/Service-oriented_architecture) O padrão de facto para a transmissão de dados nestas modernas arquiteturas de microserviços é [jSON,](https://en.wikipedia.org/wiki/JSON)que também é o meio de armazenamento para a grande maioria das bases de dados NoSQL orientadas para documentos. Isto torna as lojas de documentos NoSQL muito mais perfeitas para a persistência e sincronização [(utilizando padrões de fornecimento de eventos)](https://en.wikipedia.org/wiki/Event-driven_architecture)através de implementações complexas do Microservice. Bases de dados relacionais mais tradicionais podem ser muito mais complexas para manter nestas arquiteturas. Isto deve-se à maior quantidade de transformação necessária tanto para o estado como para a sincronização em apis. A Azure Cosmos DB em particular tem uma série de funcionalidades que o tornam ainda mais perfeito para as Microservices Architectures baseadas em JSON do que muitas bases de dados NoSQL:

* uma escolha de tipos puros de dados JSON
* um motor JavaScript e [consulta API](./javascript-query-api.md) incorporado na base de dados.
* um feed de mudança de última [geração](./change-feed.md) que os clientes podem subscrever para ser notificados de modificações num recipiente.

## <a name="some-challenges-with-nosql-databases"></a>Alguns desafios com bases de dados NoSQL

Embora existam algumas vantagens claras na implementação de bases de dados NoSQL, existem também alguns desafios que poderá querer ter em consideração. Estes podem não estar presentes ao mesmo nível quando se trabalha com o modelo relacional:

* transações com muitas relações apontando para a mesma entidade.
* transações que requerem forte consistência em todo o conjunto de dados.

Olhando para o primeiro desafio, a regra do polegar nas bases de dados NoSQL é geralmente desnormalização, que, tal como articulada anteriormente, produz leituras mais eficientes num sistema distribuído. No entanto, existem alguns desafios de design que entram em jogo com esta abordagem. Vejamos um exemplo de um produto relacionado com uma categoria e várias tags:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Backend":::

Uma abordagem de boas práticas numa base de dados de documentos NoSQL seria desnormalizar o nome da categoria e marcar nomes diretamente num "documento do produto". No entanto, para manter categorias, etiquetas e produtos sincronizados, as opções de design para facilitar isso têm uma complexidade de manutenção acrescida, porque os dados são duplicados em vários registos do produto, em vez de ser uma simples atualização numa relação "um a muitos", e uma união para recuperar os dados. 

A contrapartida é que as leituras são mais eficientes no registo desnormalizado, e tornam-se cada vez mais eficientes à medida que o número de entidades aderidas conceptualmente aumenta. No entanto, assim como a eficiência de leitura aumenta com o número crescente de entidades unidas num registo desnormalizado, também a complexidade de manutenção de manter as entidades em sintonia. Uma forma de mitigar esta compensação é criar um [modelo de dados híbrido.](./modeling-data.md#hybrid-data-models)

Embora haja mais flexibilidade disponível nas bases de dados noSQL para lidar com estas compensações, uma maior flexibilidade também pode produzir mais decisões de conceção. Consulte o nosso artigo [como modelar e dividir dados sobre a Azure Cosmos DB usando um exemplo do mundo real](./how-to-model-partition-example.md), que inclui uma abordagem para manter os [dados desnormalizados dos utilizadores em sincronização](./how-to-model-partition-example.md#denormalizing-usernames) onde os utilizadores não só se sentam em diferentes divisórias, mas em diferentes recipientes.

No que diz respeito à forte consistência, é raro que isso seja necessário em todo o conjunto de dados. No entanto, nos casos em que tal seja necessário, pode ser um desafio nas bases de dados distribuídas. Para garantir uma forte consistência, os dados precisam de ser sincronizados em todas as réplicas e regiões antes de permitir que os clientes o leiam. Isto pode aumentar a latência das leituras.

Mais uma vez, a Azure Cosmos DB oferece mais flexibilidade do que bases de dados relacionais para as várias trocas que são relevantes aqui, mas para implementações em pequena escala, esta abordagem pode adicionar mais considerações de design. Consulte o nosso artigo sobre [Consistência, disponibilidade e tradeoffs de desempenho](./consistency-levels.md) para obter mais detalhes sobre este tema.

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir a sua conta Azure Cosmos e outros conceitos:

* [Como gerir a sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhar com contentores e itens da Azure Cosmos](account-databases-containers-items.md)
* [Ponto final de serviço VNET para a sua conta Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [FIREWALL para a sua conta Azure Cosmos](how-to-configure-firewall.md)
* [Como adicionar e remover regiões do Azure na sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)