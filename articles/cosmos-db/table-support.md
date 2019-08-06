---
title: Suporte ao armazenamento de tabelas do Azure no Azure Cosmos DB
description: Saiba como a API de Tabela do Azure Cosmos DB e as Tabelas de Armazenamento do Azure funcionam em conjunto.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 08/05/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 71bc5d73a7b5bc83dc1ac835c80ac1b14d5113e9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814709"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programar com a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure

A API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure partilham o mesmo modelo de dados de tabela e expõem as mesmas operações para criar, eliminar, atualizar e consultar através dos SDKs. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Programar com a API de Tabela do Azure Cosmos DB

Neste momento, a [API de Tabela do Azure Cosmos DB](table-introduction.md) tem quatro SDKs disponíveis para programação: 

* [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): SDK do .net. Essa biblioteca tem como destino .NET Standard e tem as mesmas classes e assinaturas de método que o SDK de armazenamento público do [Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mas também tem a capacidade de se conectar a contas de Azure Cosmos DB usando o API de tabela. Os usuários da biblioteca de .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) são recomendados para atualizar para [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , pois ele está no modo de manutenção e será preterido em breve.

* [SDK do Python](table-sdk-python.md): O novo SDK Python Azure Cosmos DB é o único SDK que suporta o armazenamento de Tabelas no Python. Este SDK liga ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.

* [SDK do Java](table-sdk-java.md): Este SDK do Armazenamento do Azure tem capacidade para ligar a contas do Azure Cosmos BD através da API de Tabela.

* [SDK do node. js](table-sdk-nodejs.md): Este SDK do Armazenamento do Azure tem capacidade para ligar a contas do Azure Cosmos BD através da API de Tabela.


Informações adicionais sobre como trabalhar com o API de tabela estão disponíveis nas [perguntas frequentes: Desenvolva com o](faq.md#table) artigo API de tabela.

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


   





