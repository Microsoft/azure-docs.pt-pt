---
title: Compreender as diferenças entre o Azure Cosmos DB NoSQL e as bases de dados relacionais
description: Este artigo enumera as diferenças entre noSQL e bases de dados relacionais
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896626"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Compreender as diferenças entre o NoSQL e as bases de dados relacionais

Este artigo enumerará alguns dos principais benefícios das bases de dados noSQL sobre bases de dados relacionais. Discutiremos também alguns dos desafios no trabalho com a NoSQL. Para uma análise aprofundada das diferentes lojas de dados existentes, veja o nosso artigo sobre a escolha da loja de [dados certa.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)

## <a name="high-throughput"></a>Débito elevado

Um dos desafios mais óbvios na manutenção de um sistema de base de dados relacional é que a maioria dos motores relacionais aplicam fechaduras e trincos para impor [semântica acides](https://en.wikipedia.org/wiki/ACID)rigorosas . Esta abordagem tem benefícios em termos de garantir um estado de dados consistente dentro da base de dados. No entanto, há pesadas trocas no que diz respeito à moeda, à latência e à disponibilidade. Devido a estas restrições arquitetónicas fundamentais, os elevados volumes transacionais podem resultar na necessidade de fragmentos manualmente de dados. Implementar sharding manual pode ser um exercício demorado e doloroso.

Nestes cenários, as [bases de dados distribuídas](https://en.wikipedia.org/wiki/Distributed_database) podem oferecer uma solução mais escalável. No entanto, a manutenção ainda pode ser um exercício dispendioso e demorado. Os administradores podem ter de fazer trabalho extra para garantir que a natureza distribuída do sistema seja transparente. Podem também ter de explicar a natureza "desligada" da base de dados.

[A Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) simplifica estes desafios, ao ser implantado em todo o mundo em todas as regiões do Azure. As gamas de partição são capazes de ser dinamicamente subdivididas para aumentar perfeitamente a base de dados em linha com a aplicação, mantendo simultaneamente uma elevada disponibilidade. O multi-arrendamento de grãos finos e a governação de recursos nativos em nuvem facilita garantias surpreendentes de [latência](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) e desempenho previsível. A partilha é totalmente gerida, por isso os administradores não precisam de escrever código ou gerir divisórias.

Se os seus volumes transacionais estiverem a atingir níveis extremos, como muitos milhares de transações por segundo, deve considerar uma base de dados noSQL distribuída. Considere o Azure Cosmos DB para a máxima eficiência, facilidade de manutenção e redução do custo total de propriedade.

![Back-end](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Dados hierárquicos

Há um número significativo de casos de utilização em que as transações na base de dados podem conter muitas relações entre pais e filhos. Estas relações podem crescer significativamente com o tempo, e revelar-se difíceis de gerir. Formas de bases de [dados hierárquicas](https://en.wikipedia.org/wiki/Hierarchical_database_model) surgiram durante a década de 1980, mas não eram populares devido à ineficiência no armazenamento. Também perderam tração à medida que o [modelo relacional de Ted Codd](https://en.wikipedia.org/wiki/Relational_model) se tornou o padrão de facto usado por praticamente todos os sistemas de gestão de bases de dados mainstream.

No entanto, hoje em dia a popularidade das bases de dados de estilo documental tem crescido significativamente. Estas bases de dados podem ser consideradas uma reinvenção do paradigma hierárquico da base de dados, agora desinibida por preocupações em torno do custo de armazenamento de dados em disco. Como resultado, manter muitas relações complexas entre pais e filhos numa base de dados relacional pode agora ser considerada um anti-padrão em comparação com as abordagens modernas orientadas para documentos.

O surgimento do [design orientado para objetos,](https://en.wikipedia.org/wiki/Object-oriented_design)e o [desfasamento da impedância](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) que surge ao combinar-o com modelos relacionais, também destaca um anti-padrão nas bases de dados relacionais para determinados casos de utilização. Os custos de manutenção escondidos, mas muitas vezes significativos, podem surgir como resultado. Embora as [abordagens ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) tenham evoluído para mitigar parcialmente esta situação, as bases de dados orientadas para documentos, no entanto, acossam muito melhor com abordagens orientadas para o objeto. Com esta abordagem, os desenvolvedores não são forçados a comprometer-se com condutores orm, ou com motores de base de [dados oo específicos](https://en.wikipedia.org/wiki/Object_database)de linguagem sob medida. Se os seus dados contenham muitas relações entre pais e filhos e níveis profundos de hierarquia, é possível que deseja considerar a utilização de uma base de dados de documentos NoSQL, como a [API Azure Cosmos DB SQL](https://docs.microsoft.com/azure/cosmos-db/introduction).

![Detalhes da encomenda](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Redes e relações complexas

Ironicamente, dado o seu nome, as bases de dados relacionais apresentam uma solução menos do que ideal para modelar relações profundas e complexas. A razão para isso é que as relações entre entidades não existem realmente numa base de dados relacional. Precisam de ser calculados em tempo de execução, com relações complexas que requerem a adesão do cartesiano para permitir o mapeamento usando consultas. Como resultado, as operações tornam-se exponencialmente mais caras em termos de computação à medida que as relações aumentam. Em alguns casos, uma base de dados relacional que tente gerir essas entidades tornar-se-á inutilizável.

Várias formas de bases de dados "Network" surgiram durante o tempo em que surgiram bases de dados relacionais, mas, tal como nas bases de dados hierárquicas, estes sistemas lutaram para ganhar popularidade. A adoção lenta deveu-se à falta de casos de utilização na altura e a ineficiências de armazenamento. Hoje em dia, os motores de base de dados de gráficos podem ser considerados um ressurgimento do paradigma da base de dados da rede. O principal benefício com estes sistemas é que as relações são armazenadas como "cidadãos de primeira classe" dentro da base de dados. Assim, a travessia das relações pode ser feita em tempo constante, em vez de aumentar na complexidade do tempo com cada novo produto de adesão ou cross.

Se mantiver uma complexa rede de relações na sua base de dados, poderá considerar uma base de dados de gráficos como a [API Azure Cosmos DB Gremlin](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) para gerir estes dados.

![Graph](./media/relational-or-nosql/graph.png)

Azure Cosmos DB é um serviço de base de dados multimodelo, que oferece uma projeção API para todos os principais tipos de modelos NoSQL; Família de colunas, documento, gráfico e valor-chave. As camadas de API de [Gremlin (gráfico)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) e SQL (Core) documentam são totalmente interoperáveis. Isto tem benefícios para mudar entre diferentes modelos ao nível da programabilidade. As lojas de gráficos podem ser consultadas em termos de traversals de rede complexas, bem como transações modeladas como registos de documentos na mesma loja.

## <a name="fluid-schema"></a>Esquema fluido

Outra característica particular das bases de dados relacionais é que os schemas são obrigados a ser definidos no momento do desenho. Isto tem benefícios em termos de integridade referencial e conformidade de dados. No entanto, também pode ser restritivo à medida que o pedido cresce. Responder a alterações no esquema através de modelos logicamente separados que partilhem a mesma definição de tabela ou base de dados pode tornar-se complexo ao longo do tempo. Estes casos de utilização beneficiam frequentemente do facto de o esquema ser desviado para o pedido de gestão por registo. Isto requer que a base de dados seja "agnóstica" e permita que os registos sejam "auto-descrição" em termos dos dados neles contidos.

Se estiver a gerir dados cujas estruturas estão constantemente a mudar a um ritmo elevado, especialmente se as transações podem vir de fontes externas onde é difícil impor a conformidade em toda a base de dados, pode querer considerar uma abordagem mais schema-agnóstica utilizando um serviço de base de dados NoSQL gerido como o Azure Cosmos DB.

## <a name="microservices"></a>Microsserviços

O padrão de [microserviços](https://en.wikipedia.org/wiki/Microservices) tem crescido significativamente nos últimos anos. Este padrão tem as suas raízes na [Arquitetura Orientada para o Serviço.](https://en.wikipedia.org/wiki/Service-oriented_architecture) A norma de facto para a transmissão de dados nestas arquiteturas modernas de microserviços é a [JSON,](https://en.wikipedia.org/wiki/JSON)que também é o meio de armazenamento para a grande maioria das bases de dados noSQL orientadas para documentos. Isto faz com que o documento NoSQL armazene um ajuste muito mais perfeito tanto para a persistência como para a sincronização (utilizando padrões de sourcing de [eventos)](https://en.wikipedia.org/wiki/Event-driven_architecture)através de implementações complexas do Microservice. Bases de dados relacionais mais tradicionais podem ser muito mais complexas para manter nestas arquiteturas. Isto deve-se à maior quantidade de transformação necessária tanto para o Estado como para a sincronização através das APIs. A Azure Cosmos DB em particular tem uma série de funcionalidades que o tornam ainda mais perfeito para arquiteturas de microserviços baseadas em JSON do que muitas bases de dados NoSQL:

* uma escolha de tipos de dados JSON puros
* um motor JavaScript e [consulta API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) incorporado na base de dados.
* um alimento de [mudança](https://docs.microsoft.com/azure/cosmos-db/change-feed) de última geração que os clientes podem subscrever para ser notificado de modificações num recipiente.

## <a name="some-challenges-with-nosql-databases"></a>Alguns desafios com bases de dados NoSQL

Embora existam algumas vantagens claras na implementação de bases de dados NoSQL, existem também alguns desafios que pode querer ter em consideração. Estes podem não estar presentes ao mesmo nível quando se trabalha com o modelo relacional:

* transações com muitas relações apontando para a mesma entidade.
* transações que requerem uma forte consistência em todo o conjunto de dados.

Olhando para o primeiro desafio, a regra do polegar nas bases de dados noSQL é geralmente desnormalização, o que, tal como articulado anteriormente, produz leituras mais eficientes num sistema distribuído. No entanto, existem alguns desafios de design que entram em jogo com esta abordagem. Vejamos um exemplo de um produto relacionado com uma categoria e várias tags:

![Associações](./media/relational-or-nosql/many-joins.png)

Uma abordagem de boas práticas numa base de dados de documentos NoSQL seria desnormalizar o nome da categoria e etiquetar os nomes diretamente num "documento de produto". No entanto, para manter as categorias, etiquetas e produtos em sincronização, as opções de design para facilitar isso acrescentaram complexidade de manutenção, porque os dados são duplicados em vários registos do produto, em vez de serem uma simples atualização numa "uma a muitos" relação, e uma união para recuperar os dados. 

A compensação é que as leituras são mais eficientes no registo desnormalizado, e tornam-se cada vez mais eficientes à medida que o número de entidades aderiu conceptualmente aumenta. No entanto, tal como a eficiência da leitura aumenta com o aumento do número de entidades associadas num registo desnormalizado, também a complexidade da manutenção de manter as entidades em sincronização. Uma forma de atenuar esta compensação é criar um [modelo de dados híbridos.](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)

Embora exista mais flexibilidade disponível nas bases de dados noSQL para lidar com estas compensações, uma maior flexibilidade também pode produzir mais decisões de conceção. Consulte o nosso artigo [como modelar e dividir dados sobre o Azure Cosmos DB usando um exemplo real](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), que inclui uma abordagem para manter os dados [desnormalizados dos utilizadores em sincronização](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) onde os utilizadores não só se sentam em divisórias diferentes, mas em diferentes recipientes.

No que diz respeito a uma forte consistência, é raro que isso seja necessário em todo o conjunto de dados. No entanto, nos casos em que isso seja necessário, pode ser um desafio nas bases de dados distribuídas. Para garantir uma forte consistência, os dados precisam de ser sincronizados em todas as réplicas e regiões antes de permitir que os clientes o leiam. Isto pode aumentar a latência das leituras.

Mais uma vez, a Azure Cosmos DB oferece mais flexibilidade do que bases de dados relacionais para as várias compensações que são relevantes aqui, mas para implementações em pequena escala, esta abordagem pode acrescentar mais considerações de design. Consulte o nosso artigo sobre [Consistência, disponibilidade e trocas](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) de desempenho para mais detalhes sobre este tema.

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir a sua conta Azure Cosmos e outros conceitos:

* [Como gerir a sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhar com contentores e itens Azure Cosmos](databases-containers-items.md)
* [Ponto final do serviço VNET para a sua conta Azure Cosmos](vnet-service-endpoint.md)
* [Firewall IP para a sua conta Azure Cosmos](firewall-support.md)
* [Como adicionar e remover as regiões azure à sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
