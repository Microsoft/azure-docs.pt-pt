---
title: Introdução à API de tabelas do Azure Cosmos DB
description: Saiba como você pode usar Azure Cosmos DB para armazenar e consultar grandes volumes de dados de chave-valor com baixa latência usando a API de tabelas do Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 5b2e2c51eaa878ba0ce8bc31c001575acebe6919
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967553"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introdução ao Azure Cosmos DB: API de Tabela

O [Azure Cosmos DB](introduction.md) disponibiliza a API de Tabelas às aplicações que foram escritas para o armazenamento de Tabelas do Azure e que precisam de capacidades premium, como:

* [Distribuição global chave na mão](distribute-data-globally.md).
* [Débito dedicado](partition-data.md) em todo o mundo.
* Latências de milissegundos na ordem de um dígito no percentil 99.º.
* Elevada disponibilidade garantida.
* Indexação secundária automática.

As aplicações escritas para o armazenamento de Tabelas do Azure podem migrar para o Azure Cosmos BD com a API de Tabela sem alterações de código e tirar partido das funcionalidades premium. A API de Tabela tem SDKs do cliente disponíveis para .NET, Java, Python e Node.js.

> [!IMPORTANT]
> O SDK do .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está no modo de manutenção e será preterido em breve. Atualize para a nova biblioteca de .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a obter os recursos mais recentes com suporte no API de tabela.

## <a name="table-offerings"></a>Ofertas de Tabelas
Se utilizar atualmente o armazenamento de Tabelas do Azure, beneficia das vantagens seguintes se mudar para a API de Tabelas do Azure Cosmos DB:

| | Armazenamento de Tabelas do Azure | API de Tabelas do Azure Cosmos DB |
| --- | --- | --- |
| Latência | Rápida, mas sem limites superiores. | Latência de milissegundo de dígito único para leituras e gravações, apoiada com latência de 10 ms de < para leituras e gravações no 99 º percentil, em qualquer escala, em qualquer lugar do mundo. |
| Débito | Modelo de débito variável. As tabelas têm um limite de escalabilidade de 20 000 operações/s. | Altamente dimensionável, com [débito reservado dedicado por tabela](request-units.md), com suporte dos SLAs. As contas não têm limite superior relativamente ao débito e suportam mais de dez milhões de operações/s por tabela. |
| Distribuição global | Região única com uma região de leitura secundária opcional para elevada disponibilidade. Não pode iniciar ativações pós-falha. | [Distribuição global pronta](distribute-data-globally.md) para uso de um para qualquer número de regiões. Suporte para [ativações pós-falha automáticas e manuais](high-availability.md) em qualquer altura e em qualquer parte do mundo. Recurso de vários mestres para permitir que qualquer região aceite operações de gravação. |
| Indexação | Apenas índice primário em PartitionKey e RowKey. Sem índices secundários. | Indexação automática e completa em todas as propriedades por padrão, sem gerenciamento de índice. |
| Consulta | A execução de consultas utiliza o índice para a chave primária e analisa, se for caso disso. | As consultas podem tirar partido da indexação automática nas propriedades para tempos de consulta rápidos. |
| Consistência | Forte na região primária. Eventual na região secundária. | [Cinco níveis de consistência bem definidos](consistency-levels.md) para alternar entre disponibilidade, latência, débito e consistência com base nas necessidades da sua aplicação. |
| Preços | Otimizado para armazenamento. | Otimizado para débito. |
| SLAs | 99,9% a 99,99% de disponibilidade, dependendo da estratégia de replicação. | 99,999% de disponibilidade de leitura, 99,99% de disponibilidade de gravação em uma conta de região única e 99,999% de disponibilidade de gravação em contas de várias regiões. [SLAs abrangentes](https://azure.microsoft.com/support/legal/sla/cosmos-db/) que abrangem disponibilidade, latência, taxa de transferência e consistência. |

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
* [Azure Cosmos DB .NET Standard SDK da tabela](table-sdk-dotnet-standard.md)
* [SDK do .NET da tabela Azure Cosmos DB](table-sdk-dotnet.md)
* [SDK do Java da tabela Azure Cosmos DB](table-sdk-java.md)
* [SDK do node. js de tabela Azure Cosmos DB](table-sdk-nodejs.md)
* [SDK da Tabela do Azure Cosmos DB para Python](table-sdk-python.md)