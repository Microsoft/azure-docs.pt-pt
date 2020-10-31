---
title: Suporte de armazenamento de mesa Azure em Azure Cosmos DB
description: Saiba como a Azure Cosmos DB Table API e a Azure Storage Tables funcionam em conjunto partilhando o mesmo modelo de dados de tabela uma operação
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/21/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 5e586ae8a6b6a4010419254ce3e380f377d370f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101099"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programar com a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

A API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure partilham o mesmo modelo de dados de tabela e expõem as mesmas operações para criar, eliminar, atualizar e consultar através dos SDKs.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Programar com a API de Tabela do Azure Cosmos DB

Neste momento, a [API de Tabela do Azure Cosmos DB](table-introduction.md) tem quatro SDKs disponíveis para programação: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Esta biblioteca tem como alvo .NET Standard e tem as mesmas classes e assinaturas de métodos que o [SDK de armazenamento do Windows Azure,](https://www.nuget.org/packages/WindowsAzure.Storage)mas também tem a capacidade de se ligar às contas DB do Azure Cosmos utilizando a Tabela API. Os utilizadores da biblioteca.NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) são recomendados para fazer o upgrade para [Microsoft.Azure.Cosmos.Table,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) uma vez que está em modo de manutenção e será depreciado em breve.

* [Python SDK](table-sdk-python.md): O novo Azure Cosmos DB Python SDK é o único SDK que suporta o armazenamento da Mesa Azure em Python. Este SDK liga ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): Este Azure Storage SDK tem a capacidade de se ligar às contas DB da Azure Cosmos utilizando a Tabela API.

* [Node.js SDK](table-sdk-nodejs.md): Este Azure Storage SDK tem a capacidade de ligar-se às contas DB da Azure Cosmos utilizando a Tabela API.


Estão disponíveis informações adicionais sobre como trabalhar com a API de Tabela no artigo [FAQ: Programar com a API de Tabela](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Programar com o armazenamento de Tabelas do Azure

O armazenamento de Tabelas do Azure tem estes SDKs disponíveis para programação:

- As [bibliotecas Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft.Azure.Storage.File,](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/) [Microsoft.Azure.Storage.Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)e [Microsoft.Azure.Storage.As](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) bibliotecas comuns permitem-lhe trabalhar com o serviço de armazenamento de mesa Azure. Se estiver a utilizar a Tabela API em Azure Cosmos DB, pode utilizar a biblioteca [Microsoft.Azure.CosmosDB.Table.](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/)
- [Python SDK.](https://github.com/Azure/azure-cosmos-table-python) A Azure Cosmos DB Table SDK para Python suporta o serviço de armazenamento de mesas (porque o Azure Table Storage e a API da Tabela Cosmos DB partilham as mesmas funcionalidades e funcionalidades, e num esforço para fábricar os nossos esforços de desenvolvimento SDK, recomendamos usar este SDK).
- [Azure Storage SDK para Java](https://github.com/azure/azure-storage-java). Este SDK do Armazenamento do Azure fornece uma biblioteca de cliente no Java para consumir o armazenamento de Tabelas do Azure.
- [Node.js SDK.](https://github.com/Azure/azure-storage-node) Este SDK fornece um pacote Node.js e uma biblioteca de cliente JavaScript compatível com o browser para consumir o serviço de Tabelas de armazenamento.
- [Módulo PowerShell do AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Este módulo PowerShell tem cmdlets para trabalhar com Tabelas de armazenamento.
- [Biblioteca de Cliente do Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/). Esta biblioteca permite-lhe criar aplicações com o Armazenamento do Azure.
- [Biblioteca de Cliente das Tabelas de Armazenamento do Azure para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Este projeto fornece um pacote Ruby que torna mais fácil aceder aos serviços de Tabela de armazenamento do Azure.
- [Biblioteca de Cliente PHP das Tabelas de Armazenamento do Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Este projeto fornece uma biblioteca de cliente PHP que torna mais fácil aceder aos serviços de Tabela de armazenamento do Azure.


   





