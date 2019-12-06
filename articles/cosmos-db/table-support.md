---
title: Suporte ao armazenamento de tabelas do Azure no Azure Cosmos DB
description: Saiba como Azure Cosmos DB as tabelas de armazenamento do API de Tabela e do Azure funcionam juntas compartilhando o mesmo modelo de dados de tabela em operações
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 4f742ce34ec429f0fc822562c1ad4ad07f3e7965
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870433"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programar com a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure

A API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure partilham o mesmo modelo de dados de tabela e expõem as mesmas operações para criar, eliminar, atualizar e consultar através dos SDKs.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Programar com a API de Tabela do Azure Cosmos DB

Neste momento, a [API de Tabela do Azure Cosmos DB](table-introduction.md) tem quatro SDKs disponíveis para programação: 

* [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): SDK do .net. Essa biblioteca tem como destino .NET Standard e tem as mesmas classes e assinaturas de método que o SDK de armazenamento público do [Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mas também tem a capacidade de se conectar a contas de Azure Cosmos DB usando o API de tabela. Os usuários da biblioteca de .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) são recomendados para atualizar para [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , pois ele está no modo de manutenção e será preterido em breve.

* [SDK do Python](table-sdk-python.md): o novo SDK do Python Azure Cosmos DB é o único SDK que dá suporte ao armazenamento de tabelas do Azure em Python. Este SDK liga ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.

* [SDK do Java](table-sdk-java.md): este SDK do armazenamento do Azure tem a capacidade de se conectar a contas de Azure Cosmos DB usando o API de tabela.

* [SDK do node. js](table-sdk-nodejs.md): este SDK do armazenamento do Azure tem a capacidade de se conectar a contas de Azure Cosmos DB usando o API de tabela.


Estão disponíveis informações adicionais sobre como trabalhar com a API de Tabela no artigo [FAQ: Programar com a API de Tabela](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Programar com o armazenamento de Tabelas do Azure

O armazenamento de Tabelas do Azure tem estes SDKs disponíveis para programação:

- [SDK .NET WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). Esta biblioteca permite-lhe funcionar com o serviço de Tabelas de armazenamento.
- [SDK Python](https://github.com/Azure/azure-cosmos-table-python). O SDK da tabela Azure Cosmos DB para Python dá suporte ao serviço de armazenamento de tabelas (porque o armazenamento de tabelas do Azure e os API de Tabela de Cosmos DB compartilham os mesmos recursos e funcionalidades e, em um esforço para fatorar nossos esforços de desenvolvimento de SDK, recomendamos o uso desse SDK).
- [SDK do Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java). Este SDK do Armazenamento do Azure fornece uma biblioteca de cliente no Java para consumir o armazenamento de Tabelas do Azure.
- [SDK Node.js](https://github.com/Azure/azure-storage-node). Este SDK fornece um pacote Node.js e uma biblioteca de cliente JavaScript compatível com o browser para consumir o serviço de Tabelas de armazenamento.
- [Módulo PowerShell do AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Este módulo PowerShell tem cmdlets para trabalhar com Tabelas de armazenamento.
- [Biblioteca de Cliente do Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/). Esta biblioteca permite-lhe criar aplicações com o Armazenamento do Azure.
- [Biblioteca de Cliente das Tabelas de Armazenamento do Azure para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Este projeto fornece um pacote Ruby que torna mais fácil aceder aos serviços de Tabela de armazenamento do Azure.
- [Biblioteca de Cliente PHP das Tabelas de Armazenamento do Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Este projeto fornece uma biblioteca de cliente PHP que torna mais fácil aceder aos serviços de Tabela de armazenamento do Azure.


   





