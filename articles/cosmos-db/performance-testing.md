---
title: Desempenho e testes de escala com Azure Cosmos DB
description: Aprenda a fazer testes de escala e desempenho com o Azure Cosmos DB. Em seguida, pode avaliar a funcionalidade do Azure Cosmos DB para cenários de aplicação de alto desempenho.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313761"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Desempenho e testes de escala com Azure Cosmos DB

O desempenho e o teste de escala são um passo fundamental no desenvolvimento de aplicações. Para muitas aplicações, o nível de base de dados tem um impacto significativo no desempenho geral e na escalabilidade. Portanto, é um componente crítico do teste de desempenho. [O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é construído de propósito para escala elástica e desempenho previsível. Estas capacidades tornam-no um ótimo ajuste para aplicações que precisam de um nível de base de dados de alto desempenho. 

Este artigo é uma referência para os desenvolvedores que implementam suítes de teste de desempenho para as suas cargas de trabalho Do BD Azure Cosmos. Também pode ser usado para avaliar o Azure Cosmos DB para cenários de aplicação de alto desempenho. Centra-se principalmente nos testes de desempenho isolados da base de dados, mas também inclui as melhores práticas para aplicações de produção.

Depois de ler este artigo, poderá responder às seguintes perguntas: 

* Onde posso encontrar uma amostra .NET aplicação cliente para testes de desempenho de Azure Cosmos DB? 
* Como posso atingir altos níveis de entrada com a Azure Cosmos DB a partir da minha aplicação de cliente?

Para começar com o código, descarregue o projeto a partir da amostra de teste de desempenho do [Azure Cosmos DB.](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) 

> [!NOTE]
> O objetivo desta aplicação é demonstrar como obter o melhor desempenho da Azure Cosmos DB com um pequeno número de máquinas de clientes. O objetivo da amostra não é alcançar a capacidade máxima de entrada de Azure Cosmos DB (que pode escalar sem limites).
> 
> 

Se procura opções de configuração do lado do cliente para melhorar o desempenho do Azure Cosmos DB, consulte dicas de [desempenho do Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Executar a aplicação de teste de desempenho
A forma mais rápida de começar é compilar e executar a amostra .NET, conforme descrito nos seguintes passos. Também pode rever o código fonte e implementar configurações semelhantes nas aplicações do seu próprio cliente.

**Passo 1:** Descarregue o projeto a partir da amostra de teste de desempenho do [Azure Cosmos DB,](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)ou bifurque o repositório GitHub.

**Passo 2:** Modifique as definições para EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) em App.config.

> [!NOTE]
> Antes de fornecer coleções com alta entrada, consulte a [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para estimar os custos por coleção. A Azure Cosmos DB fatura armazenamento e entrada de faturas de forma independente numa base horária. Pode economizar custos apagando ou baixando a entrada dos seus recipientes Azure Cosmos após os testes.
> 
> 

**Passo 3:** Compile e execute a aplicação de consola a partir da linha de comando. Deve ver uma saída semelhante ao seguinte:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Passo 4 (se necessário):** A entrada reportada (RU/s) da ferramenta deve ser a mesma ou superior à entrada prevista da coleção ou de um conjunto de coleções. Se não for, aumentar o DegreeOfParallelismo em pequenos incrementos pode ajudá-lo a atingir o limite. Se a entrada dos planaltos da aplicação do seu cliente, iniciar várias instâncias da app em máquinas adicionais de clientes. Se precisar de ajuda com este passo, preencha um bilhete de apoio do [portal Azure.](https://portal.azure.com)

Depois de ter a aplicação em execução, pode experimentar [diferentes](consistency-levels.md) políticas de [indexação](index-policy.md) e níveis de consistência para entender o seu impacto na entrada e latência. Também pode rever o código fonte e implementar configurações semelhantes às suas próprias suites de teste ou aplicações de produção.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, analisámos como podes realizar testes de desempenho e escala com o Azure Cosmos DB utilizando uma aplicação de consola .NET. Para obter mais informações, veja os artigos seguintes:

* [Amostra de teste de desempenho da Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração do cliente para melhorar desempenho do Azure Cosmos DB](performance-tips.md)
* [Partição do lado do servidor em Azure Cosmos DB](partition-data.md)


