---
title: Introdução à AZure Cosmos DB Table API
description: Saiba como pode usar o Azure Cosmos DB para armazenar e consultar volumes maciços de dados de valor-chave com baixa latência utilizando a API das Tabelas Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: b3219fe3507af1c6020ad3a7d84782847dbb57ae
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275095"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introdução ao Azure Cosmos DB: Table API

O [Azure Cosmos DB](introduction.md) disponibiliza a API de Tabelas às aplicações que foram escritas para o armazenamento de Tabelas do Azure e que precisam de capacidades premium, como:

* [Distribuição global chave na mão](distribute-data-globally.md).
* [Débito dedicado](partitioning-overview.md) em todo o mundo.
* Latências de milissegundos na ordem de um dígito no percentil 99.º.
* Elevada disponibilidade garantida.
* Indexação secundária automática.

As aplicações escritas para o armazenamento de Tabelas do Azure podem migrar para o Azure Cosmos BD com a API de Tabela sem alterações de código e tirar partido das funcionalidades premium. A API de Tabela tem SDKs do cliente disponíveis para .NET, Java, Python e Node.js.

> [!IMPORTANT]
> O .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está em modo de manutenção e será deprecado em breve. Por favor, atualize para a nova biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a obter as funcionalidades mais recentes suportadas pela Tabela API.

## <a name="table-offerings"></a>Ofertas de Tabelas
Se utilizar atualmente o armazenamento de Tabelas do Azure, beneficia das vantagens seguintes se mudar para a API de Tabelas do Azure Cosmos DB:

| Funcionalidade | Armazenamento de Tabelas do Azure | API de Tabelas do Azure Cosmos DB |
| --- | --- | --- |
| Latência | Rápida, mas sem limites superiores. | Latência milissegundo de um dígito para leituras e escritas, apoiada com <10 ms latência para leituras e escritos no percentil 99, em qualquer escala, em qualquer lugar do mundo. |
| Débito | Modelo de débito variável. As tabelas têm um limite de escalabilidade de 20 000 operações/s. | Altamente dimensionável, com [débito reservado dedicado por tabela](request-units.md), com suporte dos SLAs. As contas não têm limite superior relativamente ao débito e suportam mais de dez milhões de operações/s por tabela. |
| Distribuição global | Região única com uma região de leitura secundária opcional para elevada disponibilidade. | [Distribuição global](distribute-data-globally.md) chave na mão de uma para várias regiões. Suporte para [ativações pós-falha automáticas e manuais](high-availability.md) em qualquer altura e em qualquer parte do mundo. Várias regiões de escrita para permitir que qualquer região aceite operações de escrita. |
| Indexação | Apenas índice primário em PartitionKey e RowKey. Sem índices secundários. | Indexação automática e completa de todas as propriedades por padrão, sem gestão de índices. |
| Consulta | A execução de consultas utiliza o índice para a chave primária e analisa, se for caso disso. | As consultas podem tirar partido da indexação automática nas propriedades para tempos de consulta rápidos. |
| Consistência | Forte na região primária. Eventual na região secundária. | [Cinco níveis de consistência bem definidos](consistency-levels.md) para trocar disponibilidade, latência, produção e consistência com base nas necessidades da sua aplicação. |
| Preços | Otimizado para armazenamento. | Otimizado para débito. |
| SLAs | Disponibilidade de 99,9% a 99,99%, dependendo da estratégia de replicação. | 99,999% lêem disponibilidade, 99,99% escrevem disponibilidade numa conta de uma região única e 99,999% escrevem disponibilidade em contas multi-regiões. [SLAs abrangentes](https://azure.microsoft.com/support/legal/sla/cosmos-db/) que cobrem disponibilidade, latência, produção e consistência. |

## <a name="get-started"></a>Introdução

Crie uma conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). Em seguida, comece com o nosso [Início Rápido para a API de Tabela com o .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="next-steps"></a>Passos seguintes

Eis alguns sítios por onde começar:
* [Criar uma aplicação .NET com a API de Tabela](create-table-dotnet.md)
* [Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)
* [Consulta de dados de tabela utilizando a API de Tabela](tutorial-query-table.md)
* [Learn how to set up Azure Cosmos DB global distribution by using the Table API](tutorial-global-distribution-table.md) (Como configurar a distribuição global do Azure Cosmos DB com a API de Tabela)
* [Azure Cosmos DB Table .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB Table .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB Mesa Java SDK](table-sdk-java.md)
* [Azure Cosmos DB Mesa Node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB Table SDK para Python](table-sdk-python.md)
