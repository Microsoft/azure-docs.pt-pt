---
title: Introdução à API de tabelas do Azure Cosmos DB
description: Saiba como pode utilizar o Azure Cosmos DB para armazenar e consultar grandes volumes de dados de chave-valor com baixa latência, utilizando a API de tabelas do Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/20/2017
ms.author: sngun
ms.openlocfilehash: 48c83dd77e12df86ebb68f183ad694bf8439d365
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606361"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introdução ao Azure Cosmos DB: API de Tabela

O [Azure Cosmos DB](introduction.md) disponibiliza a API de Tabelas às aplicações que foram escritas para o armazenamento de Tabelas do Azure e que precisam de capacidades premium, como:

* [Distribuição global chave na mão](distribute-data-globally.md).
* [Débito dedicado](partition-data.md) em todo o mundo.
* Latências de milissegundos na ordem de um dígito no percentil 99.º.
* Elevada disponibilidade garantida.
* [Indexação secundária automática](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

As aplicações escritas para o armazenamento de Tabelas do Azure podem migrar para o Azure Cosmos BD com a API de Tabela sem alterações de código e tirar partido das funcionalidades premium. A API de Tabela tem SDKs do cliente disponíveis para .NET, Java, Python e Node.js.

> [!IMPORTANT]
> SDK do .NET Framework [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está em manutenção modo e vão ser preteridos em breve. Atualize para a nova biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a ter as funcionalidades mais recentes suportadas pela API de tabela.

## <a name="table-offerings"></a>Ofertas de Tabelas
Se utilizar atualmente o armazenamento de Tabelas do Azure, beneficia das vantagens seguintes se mudar para a API de Tabelas do Azure Cosmos DB:

| | Armazenamento de Tabelas do Azure | API de Tabelas do Azure Cosmos DB |
| --- | --- | --- |
| Latência | Rápida, mas sem limites superiores. | Latência de milissegundos de um só dígito para leituras e escritas, suportada por leituras de latência inferiores a 10 ms e a escritas de latência inferiores a 15 ms no percentil 99, em qualquer escala e em qualquer parte do mundo. |
| Débito | Modelo de débito variável. As tabelas têm um limite de escalabilidade de 20 000 operações/s. | Altamente dimensionável, com [débito reservado dedicado por tabela](request-units.md), com suporte dos SLAs. As contas não têm limite superior relativamente ao débito e suportam mais de dez milhões de operações/s por tabela. |
| Distribuição global | Região única com uma região de leitura secundária opcional para elevada disponibilidade. Não pode iniciar ativações pós-falha. | [Distribuição global chave na mão](distribute-data-globally.md) de uma região para mais de 30. Suporte para [ativações pós-falha automáticas e manuais](high-availability.md) em qualquer altura e em qualquer parte do mundo. |
| Indexação | Apenas índice primário em PartitionKey e RowKey. Sem índices secundários. | Indexação automática e completa em todas as propriedades, sem gestão de índices. |
| Consulta | A execução de consultas utiliza o índice para a chave primária e analisa, se for caso disso. | As consultas podem tirar partido da indexação automática nas propriedades para tempos de consulta rápidos. |
| Consistência | Forte na região primária. Eventual na região secundária. | [Cinco níveis de consistência bem definidos](consistency-levels.md) para alternar entre disponibilidade, latência, débito e consistência com base nas necessidades da sua aplicação. |
| Preços | Otimizado para armazenamento. | Otimizado para débito. |
| SLAs | 99,99% de disponibilidade. | SLA de 99,99% disponibilidade para todas as contas de região única e para todas as contas de várias regiões com consistência flexível e 99,999% de disponibilidade de leitura em todas as contas de bases de dados de várias regiões [SLAs abrangentes e líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/) em disponibilidade geral. |

## <a name="get-started"></a>Introdução

Crie uma conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). Em seguida, comece com o nosso [Início Rápido para a API de Tabela com o .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="next-steps"></a>Passos Seguintes

Eis alguns sítios por onde começar:
* [Criar uma aplicação .NET com a API de Tabela](create-table-dotnet.md)
* [Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)
* [Consulta de dados de tabela utilizando a API de Tabela](tutorial-query-table.md)
* [Learn how to set up Azure Cosmos DB global distribution by using the Table API](tutorial-global-distribution-table.md) (Como configurar a distribuição global do Azure Cosmos DB com a API de Tabela)
* [API .NET da Tabela do Azure Cosmos DB](table-sdk-dotnet.md)
* [API Java de Tabelas do Azure Cosmos DB](table-sdk-java.md)
* [API Node.js de Tabelas do Azure Cosmos DB](table-sdk-nodejs.md)
* [SDK da Tabela do Azure Cosmos DB para Python](table-sdk-python.md)

