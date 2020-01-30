---
title: Suporte de armazenamento de mesa azure em Azure Cosmos DB
description: Saiba como as tabelas de armazenamento Da API e azure da Azure Da mesa de armazenamento Azure Cosmos funcionam em conjunto partilhando o mesmo modelo de dados de tabela uma operação
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76770676"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programar com a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure

A API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas do Azure partilham o mesmo modelo de dados de tabela e expõem as mesmas operações para criar, eliminar, atualizar e consultar através dos SDKs.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Programar com a API de Tabela do Azure Cosmos DB

Neste momento, a [API de Tabela do Azure Cosmos DB](table-introduction.md) tem quatro SDKs disponíveis para programação: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Esta biblioteca tem como alvo .NET Standard e tem as mesmas classes e assinaturas de métodos que o [Público Windows Azure Storage SDK,](https://www.nuget.org/packages/WindowsAzure.Storage)mas também tem a capacidade de se conectar às contas DoM Do MB da Azure utilizando a API de Mesa. Os utilizadores da biblioteca .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) são recomendados para fazer upgrade para [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) como está em modo de manutenção e será depreciado em breve.

* [Python SDK](table-sdk-python.md): O novo Azure Cosmos DB Python SDK é o único SDK que suporta o armazenamento da Mesa Azure em Python. Este SDK liga ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): Este SDK de Armazenamento Azure tem a capacidade de se ligar às contas DoM APi da Azure Cosmos utilizando a Tabela API.

* [Node.js SDK](table-sdk-nodejs.md): Este SDK de Armazenamento Azure tem a capacidade de se ligar às contas DoM Do MDS da Azure utilizando a Tabela API.


Estão disponíveis informações adicionais sobre como trabalhar com a API de Tabela no artigo [FAQ: Programar com a API de Tabela](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Programar com o armazenamento de Tabelas do Azure

O armazenamento de Tabelas do Azure tem estes SDKs disponíveis para programação:

- [SDK .NET WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). Esta biblioteca permite-lhe funcionar com o serviço de Tabelas de armazenamento.
- [SDK Python](https://github.com/Azure/azure-cosmos-table-python). O Azure Cosmos DB Table SDK for Python suporta o serviço de armazenamento de mesa (porque o Azure Table Storage e o Cosmos DB's Table API partilham as mesmas funcionalidades e funcionalidades, e num esforço para fatorizar os nossos esforços de desenvolvimento sDK, recomendamos a utilização deste SDK).
- [SDK do Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java). Este SDK do Armazenamento do Azure fornece uma biblioteca de cliente no Java para consumir o armazenamento de Tabelas do Azure.
- [SDK Node.js](https://github.com/Azure/azure-storage-node). Este SDK fornece um pacote Node.js e uma biblioteca de cliente JavaScript compatível com o browser para consumir o serviço de Tabelas de armazenamento.
- [Módulo PowerShell do AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Este módulo PowerShell tem cmdlets para trabalhar com Tabelas de armazenamento.
- [Biblioteca de Cliente do Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/). Esta biblioteca permite-lhe criar aplicações com o Armazenamento do Azure.
- [Biblioteca de Cliente das Tabelas de Armazenamento do Azure para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Este projeto fornece um pacote Ruby que torna mais fácil aceder aos serviços de Tabela de armazenamento do Azure.
- [Biblioteca de Cliente PHP das Tabelas de Armazenamento do Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Este projeto fornece uma biblioteca de cliente PHP que torna mais fácil aceder aos serviços de Tabela de armazenamento do Azure.


   





