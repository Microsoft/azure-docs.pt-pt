---
title: Desempenho e dimensionamento testes com o Azure Cosmos DB
description: Saiba como dimensionar e teste com o Azure Cosmos DB de desempenho. Em seguida, pode avaliar a funcionalidade do Azure Cosmos DB para cenários de aplicações de alto desempenho.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a5842d590a4597253bac39c0b7a6f62e6acad908
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243520"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Desempenho e dimensionamento testes com o Azure Cosmos DB

Desempenho e testes de dimensionamento é uma etapa importante no desenvolvimento de aplicativos. Para muitas aplicações, o escalão de base de dados tem um impacto significativo no desempenho e escalabilidade gerais. Portanto, é um componente crítico de testes de desempenho. [O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) criado especificamente para escala elástica e o desempenho previsível. Esses recursos tornam uma excelente escolha para aplicativos que precisam de uma camada de base de dados de elevado desempenho. 

Este artigo é uma referência para os desenvolvedores que implementam conjuntos de testes de desempenho para as cargas de trabalho do Azure Cosmos DB. Também pode ser utilizado para avaliar o Azure Cosmos DB para cenários de aplicações de alto desempenho. Ele se concentra principalmente em testes de desempenho isolado da base de dados, mas também inclui práticas recomendadas para aplicações de produção.

Depois de ler este artigo, será capaz de responder às seguintes perguntas: 

* Onde posso encontrar uma aplicação de cliente .NET de exemplo para testes de desempenho do Azure Cosmos DB? 
* Como obter níveis de débito elevado com o Azure Cosmos DB a partir do meu aplicativo de cliente?

Para começar a utilizar com o código, transfira o projeto a partir [exemplo de teste de desempenho de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> O objetivo desta aplicação é demonstrar como obter o melhor desempenho do Azure Cosmos DB com um pequeno número de computadores cliente. O objetivo do exemplo é não conseguir a capacidade de débito de pico do Azure Cosmos DB (o que pode dimensionar sem limites de).
> 
> 

Se estiver à procura de opções de configuração do lado do cliente melhorar o desempenho do Azure Cosmos DB, veja [sugestões de desempenho do Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Executar o aplicativo de teste de desempenho
A forma mais rápida para começar a utilizar é compilar e executar o exemplo de .NET, conforme descrito nos passos seguintes. Também pode rever o código-fonte e implementar configurações semelhantes no seus próprios aplicativos de cliente.

**Passo 1:** Transfira o projeto de [exemplo de teste de desempenho de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), ou bifurcar o repositório do GitHub.

**Passo 2:** Modifica as definições para EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) no App. config.

> [!NOTE]
> Antes de aprovisionar coleções com um débito elevado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para estimar os custos por coleção. O Azure Cosmos DB cobra o armazenamento e débito de forma independente numa base horária. Pode poupar eliminando ou reduzir o débito de seus conjuntos do Azure Cosmos DB após os testes.
> 
> 

**Passo 3:** Compile e execute a aplicação de consola da linha de comando. Deverá ver um resultado semelhante ao seguinte:

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


**Passo 4 (se necessário):** O débito comunicado (RU/s) a partir da ferramenta deve ser igual ou maior do que o débito aprovisionado da coleção ou um conjunto de coleções. Se não for, aumentando a DegreeOfParallelism em pequenos incrementos pode ajudá-lo a atingir o limite. Se o débito da sua aplicação de cliente plateaus, inicie várias instâncias da aplicação em máquinas de cliente adicionais. Se precisar de ajuda com este passo, envie um e-mail askcosmosdb@microsoft.com ou enviar um pedido de suporte do [portal do Azure](https://portal.azure.com).

Depois de ter a aplicação em execução, pode experimentar diferentes [políticas de indexação](index-policy.md) e [níveis de consistência](consistency-levels.md) para compreender seu impacto no débito e latência. Também pode rever o código-fonte e implementar configurações semelhantes para seus próprios pacotes de testes ou aplicações de produção.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, vimos como pode executar o desempenho e dimensionamento testes com o Azure Cosmos DB ao utilizar uma aplicação de consola .NET. Para obter mais informações, veja os artigos seguintes:

* [Desempenho do Cosmos DB do Azure, teste de exemplo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração de cliente para melhorar o desempenho do Azure Cosmos DB](performance-tips.md)
* [Criação de partições do lado do servidor no Azure Cosmos DB](partition-data.md)


